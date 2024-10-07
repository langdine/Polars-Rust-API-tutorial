# Constructing a Series

A `Series` is a data structure used to store fields, containing all elements of a field, with the requirement that all elements are of the same type. Essentially, a `Series` is a `ChunkedArray`, which is an array stored in chunks. In Polars, data within a `Series` is stored in the form of chunks, where each chunk is an independent array. This design enhances data processing efficiency, especially during large-scale data operations, as chunked data can be processed in parallel.

Here's how you can construct a `Series`:

```rust
use polars::prelude::*;
let s = Series::new("field_name", vec![0i32, 2, 1, 3, 8]);
```

In this example, a `Series` named "field_name" is created, containing integer elements.