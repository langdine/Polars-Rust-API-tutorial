# Writing to JSON

## DataFrame to File

JSON files can be in two formats: `JsonFormat::Json` and `JsonFormat::JsonLines`. For more details, see [Creating a DataFrame from JSON.md](IO-从Json创建Dataframe.md).

```rust
// Write to a JSON file
let mut file = File::create("docs/data/output.json").expect("could not create file");
JsonWriter::new(&mut file)
    .with_json_format(JsonFormat::Json) // JsonFormat::Json or JsonFormat::JsonLines
    .finish(&mut employee_df)?;
```

## LazyFrame to File

```rust
let mut opt = JsonWriterOptions::default();
opt.maintain_order = true; // Data is processed in parallel; if this option is not enabled, the order of rows in the file cannot be guaranteed.
employee_df.lazy().sink_json("d:/out.json", opt)?;
// The default format is JsonLines, and it cannot currently be set to Json format.
```