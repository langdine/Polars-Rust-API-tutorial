# Reading a Single Element

To index into a `Series`, you must first cast it to a `ChunkedArray<T>` of the specified type:

```rust
let s = Series::new("field_name", [0i32, 1, 8]);
let item = s.i32().unwrap().get(2).unwrap();
```

If you need to process elements in a `Series` within a loop, the best practice is to use functions like `series.i32()` to extract a reference to the underlying `ChunkedArray<T>`. This does not copy the data. You can then perform operations on this `ChunkedArray`.

Available casting functions include: `i8() i16() i32() i64() f32() f64() u8() u16() u32() u64() bool() str() binary() decimal() list()`. These functions extract a reference to the underlying `ChunkedArray<T>` of the `Series`. You must ensure that the casting function you call matches the underlying type `T`, otherwise a runtime error will occur. To change the underlying type `T`, use the `Series::cast(&DataType::Int64)` function, which converts the type of the underlying `ChunkedArray<T>` and returns a new `Series`.