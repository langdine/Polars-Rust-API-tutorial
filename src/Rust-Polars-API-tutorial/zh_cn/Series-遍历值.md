# 遍历值

Series本身是动态类型变量。如果编程时类型未知，则必须使用Series:iter()迭代器，它会返回AnyValue封装后的值，封装和解包会产生性能损失。尽量避免返回AnyValue的API。

## 底层类型未知

```rust
let s = Series::new("foo".into(), [1i32 ,2, 3]);
let s_squared: Series = s.iter()
    .map(|opt_v| {
        match opt_v {
            AnyValue::Int32(x) => Some(x*x),
            //可以添加不同类型的处理逻辑。
            _ => None,
        }
}).collect();
```

## 底层类型已知

如果底层类型已经知晓，可以用`i8() i16() i32() i64() f32() f64() u8() u16() u32() u64() bool() str() binary() decimal() list()`这些向下转型函数，取出Series底层的Chunkedarray\<T>引用。类型解析操作只是对底层的Chunkedarray\<T>引用做了类型转换，并不会复制一份数据。另外你需要自己保证调用的解析函数和底层的T类型一致，否则会出现运行时错误。
`s.i32()?.iter()`生成对应的迭代器。

```rust
let s = Series::new("foo", [1i32 ,2, 3]);
let s_squared: Series = s.i32()?.iter()
    .map(|opt_v| {
        match opt_v {
            Some(v) => Some(v * v),
            None => None, // null value
        }
}).collect();
```
