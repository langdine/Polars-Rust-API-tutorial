# Creating a DataFrame from CSV

```rust
// Requires the polars-io feature to create a DataFrame from a CSV file
use polars::prelude::*;
// Loads into memory immediately. If the CSV content is large, consider using the lazy API
let path = "E:\\myfile\\src\\pol\\input收治病人数据.csv";
let df = CsvReadOptions::default()
    .try_into_reader_with_file_path(Some(path.into()))
    .unwrap()
    .finish()
    .unwrap();
```