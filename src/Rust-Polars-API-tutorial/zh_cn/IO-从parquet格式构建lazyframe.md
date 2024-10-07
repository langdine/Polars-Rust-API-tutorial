# 从parquet格式构建lazyframe

```rust
let mut opt = ScanArgsParquet::default();
opt.n_rows=None; //默认为None意为读取所有行，Some(100)只读取100行
opt.row_index=Some(RowIndex{name:"RowIndex".into(),offset:0});//将行索引添加到第一列，列名为RowIndex。默认为None表示不添加行索引。
let lf = LazyFrame::scan_parquet("d:/output.parquet", opt)?;
println!("{}",lf.collect()?);
```