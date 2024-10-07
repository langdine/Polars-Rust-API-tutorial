# Building a LazyFrame from Parquet Format

```rust
let mut opt = ScanArgsParquet::default();
opt.n_rows = None; // Default is None, meaning all rows are read. Some(100) would read only 100 rows.
opt.row_index = Some(RowIndex { name: "RowIndex".into(), offset: 0 }); // Adds a row index as the first column, with the column name "RowIndex". Default is None, meaning no row index is added.
let lf = LazyFrame::scan_parquet("d:/output.parquet", opt)?;
println!("{}", lf.collect()?);
```