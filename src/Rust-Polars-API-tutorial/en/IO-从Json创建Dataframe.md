# Creating a DataFrame from JSON

JSON supports two formats:

Type | Description
--|--
JsonFormat::Json | Indicates that the entire file contains an array, where the array's content consists of individual JSON objects.
JsonFormat::JsonLines | Each line contains a single JSON object.

## Json

Read Json file.
```rust
use polars::prelude::*;
// One JSON array stored in the entire file
let json_array = r#"
[
{  "json_a": 1,         "b": 2,       "c": 3},
{  "json_a": 21,   "b": 22,    "c": 23},
{  "json_a": 31,   "b": 32,    "c": 33}
]"#;
let buf = Cursor::new(json_array); // Any type implementing the Read trait can be used as input
let df = JsonReader::new(buf)
    .with_json_format(JsonFormat::Json)
    .finish()?;
```

## JsonLines

Read JsonLines file.

```rust
// One JSON object per line
let json_lines = r#"
    {"jsonlines_a": 1,"b": 2,"c": 3}
    {"jsonlines_a": 21,"b": 22,"c": 23}
    {"jsonlines_a": 31,"b": 32,"c": 33}"#;
let buf1 = Cursor::new(json_lines); // Any type implementing the Read trait can be used as input
let df1 = JsonReader::new(buf1)
    .with_json_format(JsonFormat::JsonLines)
    .finish()?;
println!("{}\n{}", df, df1);
```