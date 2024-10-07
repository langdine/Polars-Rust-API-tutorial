# 行迭代

## 推荐的行迭代方式

通常在数据编程时明确知道字段类型，此时应使用`i8() i16() i32() i64() f32() f64() u8() u16() u32() u64() bool() str() binary() decimal() list()`这些向下转型函数，取出Series底层的`Chunkedarray<T>`引用。类型解析操作只是对底层的`Chunkedarray<T>`引用做了类型转换，并不会复制一份数据。另外你需要自己保证调用的解析函数和底层的T类型一致，否则会出现运行时错误。参见：[遍历Series](Series-遍历值.md#底层类型已知)

分别取出每个字段的元素迭代器后应使用`itertools::multizip`将多个迭代器绑定成一个，然后进行迭代。
这里给出通过遍历Dataframe行，生成Person类型值的代码。

```rust
    use polars::prelude::*;
    use itertools::multizip;
    #[derive(Debug)]
    pub struct Person {
        id: u32,
        name: String,
        age: u32,
    }
    let df = df!(
        "id" => &[1u32,2,3],
        "name" => &["John", "Jane", "Bobby"],
        "age" => &[32u32, 28, 45]
    )
    .unwrap();
    //take_columns()将获取df的所有权，这不是必须的，df.columns可返回指定字段的引用。
    let objects = df.take_columns();
    //对字段进行向下转型，并生成迭代器
    let id_ = objects[0].u32()?.iter();
    let name_ = objects[1].str()?.iter();
    let age_=objects[2].u32()?.iter();
    //利用multizip同时迭代多个迭代器
    let combined = multizip((id_, name_, age_));
    let res: Vec<_>= combined.map(
        |(a, b, c)|{
            Person{
                id:a.unwrap(),
                name:b.unwrap().to_owned(),
                age:c.unwrap(),
            }
        }).collect();
       print!("{:?}",res);
```

## 不推荐的行迭代方式，AnyValue

在Rust的Polars库中，`Series`是一个动态类型的数据结构，它可以包含任何类型的数据。当你使用df.get_row()方法获取一行时，数据会被逐个封装进AnyValue。Polars需要在运行时确定这个元素的实际类型。AnyValue是枚举，你需要用模式匹配将值取出。这个过程需要一些额外的计算，因此如果在循环中使用`df.get_row()`方法，这些额外的计算会累积起来，可能会显著降低代码的性能。

这里给出通过df.get_row遍历Dataframe行，生成Person类型值的代码。
```rust
use polars::prelude::*;
#[derive(Debug)]
pub struct Person {
    id: u32,
    name: String,
    age: u32,
}
let df = df!(
    "id" => &[1u32,2,3],
    "name" => &["John", "Jane", "Bobby"],
    "age" => &[32u32, 28, 45]
 ).unwrap();

    let personlist_iter=
(0..df.height()).into_iter().map(
    |x: usize|{
        let mut row_ = df.get_row(x).unwrap();
        let mut row_iter= row_.0.into_iter();
        //通过模式匹配取出对应的值
        if let (AnyValue::UInt32(id_),
                 AnyValue::String(name_),
                 AnyValue::UInt32(age_)) = 
                (row_iter.next().unwrap(),
                row_iter.next().unwrap(),
                row_iter.next().unwrap()){
                    return Person{
                        id: id_,
                         name: name_.to_string(),
                         age: age_,
                        };
                    }else {
                        panic!("bad value in df!");
                    }
 } );
    let person_list: Vec<Person>=personlist_iter.collect::<Vec<_>>();
println!("{:?}",person_list);
```