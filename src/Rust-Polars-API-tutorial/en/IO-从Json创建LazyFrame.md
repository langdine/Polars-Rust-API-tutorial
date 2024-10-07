# Creating a LazyFrame from JSON

This is used for reading JSON line files in a lazy manner. The regular method of reading files loads the data into memory immediately, which can consume unnecessary resources if the JSON file is very large. The Lazy API defers the actual reading until the `collect()` method of the LazyFrame is called. Before calling the `collect()` function, you can set processing methods and computation expressions.

```rust
// Requires the polars-io feature to create a lazyframe from a JSON file
use polars::prelude::*;
let lazyreader = LazyJsonLineReader::new("./test.csv");
let lazyreader = LazyJsonLineReader::new_paths(&["./test0.csv", "./test1.csv"]); // Reading multiple files.
let lf = lazyreader.finish()?;
```

## LazyJsonLineReader API

API | Description
--|--
with_row_index(self, row_index: Option\<RowIndex>) | Adds a row index after reading, starting from 0. `RowIndex{name:"RowIndex", offset:0}`
with_n_rows(num_rows: Option\<usize>) | Reads only n rows; cannot guarantee exact n in multithreaded conditions.
with_schema_overwrite(self, schema_overwrite) | Sets the type for certain fields.
finish() | Obtains the final lazyframe.