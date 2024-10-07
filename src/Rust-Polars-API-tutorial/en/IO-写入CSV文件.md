# Writing to a CSV File

## DataFrame to File

```rust
// Write to a CSV file
let mut file = File::create("docs/data/output.csv").expect("could not create file");
CsvWriter::new(&mut file).finish(&mut employee_df)?;
```

## LazyFrame to File

```rust
let mut opt = CsvWriterOptions::default();
opt.maintain_order = true; // Data is processed in parallel; if this option is not enabled, the order of rows in the file cannot be guaranteed.
opt.include_bom = true; // Add BOM. If BOM is not added, Excel may display garbled text; some programs do not support UTF-8 BOM, which can also lead to garbled text.
df1.lazy().sink_csv("d:/out.csv", opt)?;
```

BOM stands for Byte Order Mark. It is intended for UTF-16 and UTF-32 to indicate byte order. UTF-16 and UTF-32 process data in units of 2 and 4 bytes, respectively, which requires consideration of endianness (big-endian or little-endian). UTF-8 processes data in units of 1 byte and is not affected by CPU endianness; therefore, UTF-8 does not need a BOM to indicate byte order. A UTF-8 without a BOM is the standard format. However, Microsoft uses BOM to indicate encoding, so a UTF-8 CSV format without a BOM may display garbled text in Excel, as Excel defaults to a different encoding. UTF-8 with a BOM can also have compatibility issues, as some programs do not recognize BOM, leading to UTF-8 garbled text.