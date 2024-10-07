# Schema

In Polars, a schema refers to the structure of a data table, including detailed information about field names and field types. Typically, when loading a CSV, Polars can automatically infer data types. However, there are occasions when this may not meet your needs, and you need to specify types manually, such as when parsing dates and times. In the `.with_dtype_overwrite` call, you don't need to specify information for all fields; unspecified fields will still be automatically inferred. For available data types, see [Data Types](基本概念.md#数据类型).

```rust
// This code demonstrates how to specify the types for certain fields in a CSV.
use polars::prelude::*;
use std::fs::File;

let mut schema = Schema::default();
schema.insert("col1".into(), DataType::String);
schema.insert("col2".into(), DataType::Datetime(TimeUnit::Milliseconds, None));
// Polars can automatically recognize standard time formats like "2024-09-20 00:44:00+08:00", where +08:00 indicates the timezone. If the timezone part is omitted, it defaults to UTC.
schema.insert("col3".into(), DataType::Categorical(None, CategoricalOrdering::Physical));

let lazyreader = LazyCsvReader::new("E:\\data.csv")
    .with_has_header(true)
    .with_dtype_overwrite(Some(schema.into())); // Set field types based on column names.
    .finish()?;
```
