# Creating a LazyFrame from CSV

This is used for reading CSV files in a lazy manner. The regular method of reading files loads the data into memory immediately, which can consume unnecessary resources if the CSV file is very large. The Lazy API defers the actual reading until the `collect()` method of the lazy DataFrame is called. Before calling the `collect()` function, you can set processing methods and computation expressions.

```rust
// Requires the polars-io feature to create a lazyframe from a CSV file
use polars::prelude::*;
let lazyreader = LazyCsvReader::new("./test.csv");
let lazyreader = LazyCsvReader::new_paths(&["./test0.csv", "./test1.csv"]); // Reading multiple files.
let lf = lazyreader.finish()?;
```

## LazyCsvReader API

API | Description
--|--
with_glob(toggle: bool) | Enables glob pattern matching for the path.
with_skip_rows_after_header(self, offset: usize) | Skips a number of rows after the header.
with_row_index(self, row_index: Option\<RowIndex>) | Adds a row index after reading, starting from 0. `RowIndex{name:"RowIndex", offset:0}`
with_n_rows(num_rows: Option\<usize>) | Reads only n rows; cannot guarantee exact n in multithreaded conditions.
with_skip_rows(n: usize) | Skips n rows; the header starts from row index n.
with_has_header(has_header: bool) | Indicates whether there is a header row.
with_separator(separator: u8) | Default field separator.
with_comment_prefix(comment_prefix: Option\<&str>) | Comment marker; lines starting with `comment_prefix` are ignored as comments.
with_quote_char(quote_char: Option\<u8>) | String quote marker, e.g., `b'"'`.
with_eol_char(eol_char: u8) | End-of-line character, e.g., `b'\n'`.
with_null_values(null_values: Option\<NullValues>) | Sets strings recognized as null values.
with_encoding(CsvEncoding::Utf8) | Sets the character encoding.
finish() | Obtains the final lazyframe.