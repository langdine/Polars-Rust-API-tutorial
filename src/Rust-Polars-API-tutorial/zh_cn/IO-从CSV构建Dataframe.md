# 从CSV构建Dataframe

```rust
//需要polars-io特性，从csv文件创建，lazy dataframe
use polars::prelude::*;

//立即载入内存。如果csv内容很大，应该考虑使用lazy api
let path="E:\\myfile\\src\\pol\\input收治病人数据.csv";
let df = CsvReadOptions::default()
    .try_into_reader_with_file_path(Some(path.into()))
    .unwrap()
    .finish()
    .unwrap();
```