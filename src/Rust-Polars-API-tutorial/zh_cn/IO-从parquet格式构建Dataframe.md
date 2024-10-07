# 从parquet格式构建Dataframe

Apache Parquet是一种开源的列式存储数据文件格式，旨在高效存储和检索数据。它提供了高效的数据压缩和编码方案，能够处理大量复杂数据。同时，支持多种编程语言。与简单的CSV格式相比，Parquet在存储和处理大数据集时具有明显优势：

- 存储效率：Parquet体积比CSV小得多。支持多种数据压缩。
- 查询性能：Parquet的导入和查询速度远高于CSV，特别是在处理大数据时。
- 自描述：Parquet 文件包含有关数据结构的元数据，包含字段和字段类型的详细信息。
- 兼容性和性能：Parquet受到许多数据处理框架（如 Hadoop 和 Spark）的兼容

```rust
//本代码演示如何加载Parquet格式文件
use std::fs::File;
use polars::prelude::*;
let mut input_file=File::open("d:/output.parquet")?;
let df = ParquetReader::new(&mut input_file).finish()?;
println!("{}",df);
```
