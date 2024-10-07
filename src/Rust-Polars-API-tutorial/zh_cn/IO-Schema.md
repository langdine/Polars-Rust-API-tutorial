# Schema

Schema在polars指的是数据表的结构，包含字段名和字段类型的详细信息。通常在csv载入时，polars能自动猜测数据类型。但偶尔会不满足需要，需要手动指明类型。例如解析日期时间。在.with_dtype_overwrite调用中不需要指明全部字段的信息，未指明的字段仍会自动猜测。可用的数据类型参见[数据类型](基本概念.md#数据类型)

```rust
// 本段代码演示如何指定csv部分字段的类型。
use polars::prelude::*;
use std::fs::File;
let mut schema = Schema::default();
    schema.insert("col1".into(),DataType::String);
    schema.insert("col2".into(),DataType::Datetime( TimeUnit::Milliseconds, None));
    // polars 能自动识别形如"2024-09-20 00:44:00+08:00"的标准时间格式，其中+08:00表示时区，如省略时区部分则默认为UTC时区
    schema.insert("col3".into(), DataType::Categorical(None, CategoricalOrdering::Physical ));
let lazyreader=LazyCsvReader::new("E:\\data.csv")
    .with_has_header(true)
    .with_dtype_overwrite(Some(schema.into()));//根据列名设置字段类型。
    .finish()?;
```