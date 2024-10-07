# Constructing Lazyframe

A Lazyframe can be constructed by loading from a file using the APIs introduced in the [IO Chapter](IO.md). Alternatively, you can generate a corresponding Lazyframe by calling the `lazy()` method on a DataFrame.

```rust
let lf = df.lazy();
```