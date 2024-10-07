# 写入CSV文件

## Dataframe to file

```rust
//写入csv文件
let mut file = File::create("docs/data/output.csv").expect("could not create file");
CsvWriter::new(&mut file).finish(&mut employee_df)?;
```

## lazyframe to file

```rust
let mut opt= CsvWriterOptions::default();
opt.maintain_order=true; //数据是并行处理的，如不开启此选项，文件中行顺序不能保证
opt.include_bom=true;//添加BOM 。如果不添加BOM，Excel打开会乱码;某些程序不支持UTF8 BOM，此时也会乱码。
df1.lazy().sink_csv("d:/out.csv", opt)?; 
```
BOM即Byte Order Mark字节序标记。BOM是为UTF-16和UTF-32准备的，用户标记字节序（byte order）。BOM即Byte Order Mark字节序标记。UTF-16、UTF-32是以2个字节和4个字节为单位进行处理的，就要考虑大端序、小端序问题。UTF-8编码是以1个字节为单位进行处理的，不会受CPU大小端的影响；UTF-8不需要BOM来表明字节顺序，不包含BOM的UTF-8才是标准格式。但微软选择用BOM来表明编码方式，因此没有BOM的UTF-8 CSV格式Excel会乱码，因为Excel默认编码为。含有BOM的UTF8格式也有兼容性问题，某些程序不识别BOM，就会导致UTF-8乱码。