# 写入parquet格式

## Dataframe to file

```rust

use std::fs::File;
use polars::prelude::*;
let mut file = File::create("d:/output.parquet").expect("could not create file");
ParquetWriter::new(& mut file).with_compression(ParquetCompression::Zstd(None)).finish(& mut employee_df)?;
```

## lazyframe to file

```rust
let mut opt = ParquetWriteOptions {
    compression: ParquetCompression::Zstd(None),//启用压缩
    maintain_order: true,//保持行顺序，默认多线程操作行序无法保证
    ..Default::default()
};
employee_df.lazy().sink_parquet("D:/output.parquet",opt)?;
```
