# Writing to Parquet Format

## DataFrame to File

```rust
use std::fs::File;
use polars::prelude::*;
let mut file = File::create("d:/output.parquet").expect("could not create file");
ParquetWriter::new(&mut file)
    .with_compression(ParquetCompression::Zstd(None))
    .finish(&mut employee_df)?;
```

## LazyFrame to File

```rust
let mut opt = ParquetWriteOptions {
    compression: ParquetCompression::Zstd(None), // Enable compression
    maintain_order: true, // Maintain row order; by default, multithreaded operations cannot guarantee row order
    ..Default::default()
};
employee_df.lazy().sink_parquet("D:/output.parquet", opt)?;
```