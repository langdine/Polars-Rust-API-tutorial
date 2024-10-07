# 读取单个元素

Series必须先解析为指定类型的ChunkedArray才能进行索引：

```rust
let s = Series::new("字段名", [0i32, 1, 8]);
let item =x.i32().unwrap().get(2).unwrap();
```

如果你需要在循环中处理`Series`中的元素，最好的做法是使用series.i32() 等系列函数，取出底层的Chunkedarray\<T>引用，并不会复制一份数据。然后在这个`ChunkedArray`上进行操作。
可用的解析函数：`i8() i16() i32() i64() f32() f64() u8() u16() u32() u64() bool() str() binary() decimal() list()`，它们取出Series底层的Chunkedarray\<T>引用。另外你需要自己保证调用的解析函数和底层的T类型一致，否则会出现运行时错误。转换底层类型T使用Series::cast(&DataType::Int64)函数，他会转换底层Chunkedarray\<T>的T的类型，返回新的Series。
