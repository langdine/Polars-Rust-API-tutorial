# Row Iteration

## Recommended Method for Row Iteration

In data programming, you often know the field types explicitly. In such cases, you should use the downcasting functions like `i8()`, `i16()`, `i32()`, `i64()`, `f32()`, `f64()`, `u8()`, `u16()`, `u32()`, `u64()`, `bool()`, `str()`, `binary()`, `decimal()`, `list()` to extract references to the underlying `ChunkedArray<T>` of a Series. The type parsing operation only performs a type conversion on the underlying `ChunkedArray<T>` reference and does not copy the data. You need to ensure that the parsing function you call matches the underlying type T; otherwise, a runtime error will occur. See: [Iterating over Series](Series-遍历值.md).

After extracting the element iterators for each field, use `itertools::multizip` to bind multiple iterators into one and then iterate over them. Here is the code to generate `Person` type values by iterating over DataFrame rows.

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
    "id" => &[1u32, 2, 3],
    "name" => &["John", "Jane", "Bobby"],
    "age" => &[32u32, 28, 45]
)
.unwrap();
// take_columns() will take ownership of df, which is not necessary; df.columns can return references to specified fields.
let objects = df.take_columns();
// Downcast fields and generate iterators
let id_ = objects[0].u32()?.iter();
let name_ = objects[1].str()?.iter();
let age_ = objects[2].u32()?.iter();
// Use multizip to iterate over multiple iterators simultaneously
let combined = multizip((id_, name_, age_));
let res: Vec<_> = combined.map(
    |(a, b, c)| {
        Person {
            id: a.unwrap(),
            name: b.unwrap().to_owned(),
            age: c.unwrap(),
        }
    }).collect();
print!("{:?}", res);
```

## Not Recommended Method for Row Iteration, AnyValue

In the Rust Polars library, `Series` is a dynamically typed data structure that can contain any type of data. When you use the `df.get_row()` method to get a row, the data is encapsulated into `AnyValue` one by one. Polars needs to determine the actual type of this element at runtime. `AnyValue` is an enumeration, and you need to use pattern matching to extract the value. This process requires some additional computation, so if you use the `df.get_row()` method in a loop, these extra computations can accumulate and significantly degrade the performance of your code.

Here is the code to generate `Person` type values by iterating over DataFrame rows using `df.get_row`.

```rust
use polars::prelude::*;
#[derive(Debug)]
pub struct Person {
    id: u32,
    name: String,
    age: u32,
}
let df = df!(
    "id" => &[1u32, 2, 3],
    "name" => &["John", "Jane", "Bobby"],
    "age" => &[32u32, 28, 45]
).unwrap();
let personlist_iter =
(0..df.height()).into_iter().map(
    |x: usize| {
        let mut row_ = df.get_row(x).unwrap();
        let mut row_iter = row_.0.into_iter();
        // Extract the corresponding values using pattern matching
        if let (AnyValue::UInt32(id_),
                AnyValue::String(name_),
                AnyValue::UInt32(age_)) =
                (row_iter.next().unwrap(),
                row_iter.next().unwrap(),
                row_iter.next().unwrap()) {
                    return Person {
                        id: id_,
                        name: name_.to_string(),
                        age: age_,
                    };
                } else {
                    panic!("bad value in df!");
                }
});
let person_list: Vec<Person> = personlist_iter.collect::<Vec<_>>();
println!("{:?}", person_list);
```
