# 从Json创建Dataframe

Json支持两种格式的Json文件：

类型|含义
--|--
JsonFormat::Json|表示整个文件中包含一个数组，数组内容是一个一个的Json object。
JsonFormat::JsonLines| 一行一个Json object。

## Json

```rust
use polars::prelude::*;
    // one json array stored in entire file
let json_array=r#"
[
{  "json_a": 1,         "b": 2,       "c": 3},
{  "json_a": 21,   "b": 22,    "c": 23},
{  "json_a": 31,   "b": 32,    "c": 33}
]"#;
let buf=Cursor::new(json_array);//实现Read trait都可以作为输入
let df = JsonReader::new(buf)
    .with_json_format(JsonFormat::Json)
    .finish()?;
```

## JsonLines

```rust
//one json object per line;
let json_lines=r#" 
    {"jsonlines_a": 1,"b": 2,"c": 3}
    {"jsonlines_a": 21,"b": 22,"c": 23}
    {"jsonlines_a": 31,"b": 32,"c": 33}"#;
let buf1=Cursor::new(json_lines);//实现Read trait都可以作为输入
let df1 = JsonReader::new(buf1)
    .with_json_format(JsonFormat::JsonLines)
    .finish()?;

    println!("{}\n{}",df,df1);
```
