# Iterating Over Values

A Series is a dynamically typed variable. If the type is unknown during programming, you must use the `Series:iter()` iterator, which returns values wrapped in `AnyValue`. This wrapping and unwrapping can cause performance loss. Try to avoid APIs that return `AnyValue`.

## Iterating Over AnyValue

```rust
let s = Series::new("foo".into(), [1i32, 2, 3]);
let s_squared: Series = s.iter()
    .map(|opt_v| {
        match opt_v {
            AnyValue::Int32(x) => Some(x * x),
            // You can add handling logic for different types.
            _ => None,
        }
}).collect();
```

## Iterating Over Type Known

If the underlying type is known, you can use downcasting functions like `i8() i16() i32() i64() f32() f64() u8() u16() u32() u64() bool() str() binary() decimal() list()` to extract a reference to the underlying `ChunkedArray<T>` of the Series. The type casting operation only converts the reference to the underlying `ChunkedArray<T>` and does not copy the data. Additionally, you need to ensure that the casting function you call matches the underlying type `T`, otherwise a runtime error will occur. `s.i32()?.iter()` generates the corresponding iterator.

```rust
let s = Series::new("foo", [1i32, 2, 3]);
let s_squared: Series = s.i32()?.iter()
    .map(|opt_v| {
        match opt_v {
            Some(v) => Some(v * v),
            None => None, // null value
        }
}).collect();
```