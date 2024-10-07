# 写入Json

## Dataframe to file

Json文件有两种格式JsonFormat::Json和JsonFormat::Json，详情见[从Json创建Dataframe.md](IO-从Json创建Dataframe.md)

```rust
//写入Json文件
let mut file = File::create("docs/data/output.json").expect("could not create file");
JsonWriter::new(&mut file)
    .with_json_format(JsonFormat::Json) //JsonFormat::Json 或 JsonFormat::JsonLines 
    .finish(&mut employee_df)?;
```

## lazyframe to file

```rust
let mut opt= JsonWriterOptions::default();
opt.maintain_order=true; //数据是并行处理的，如不开启此选项，文件中行顺序不能保证
employee_df.lazy().sink_json("d:/out.json", opt)?;
//默认为JsonLines格式，暂时无法设置为Json格式。
```
