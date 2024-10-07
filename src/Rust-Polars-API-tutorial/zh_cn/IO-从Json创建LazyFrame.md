# 从Json创建LazyFrame

用于lazy方式读取Jsonline文件，普通读取文件的方式会立刻将数据载入内存。对于Json文件非常大来说会占用不必要的资源。Lazy API将事实读取推迟到LazyFrame的collect()方法调用。在collect()函数调用之前，你可以设置处理方式和计算表达式。

```rust
//需要polars-io特性，从Json文件创建，lazyframe
use polars::prelude::*;
let lazyreader=LazyJsonLineReader::new("./test.csv");
let lazyreader=LazyJsonLineReader::new_paths(&["./test0.csv","./test1.csv"]); 读取多个文件。
let lf= lazyreader.finish()?;
```

## LazyJsonLineReader API

API|含义
--|--
with_row_index(self, row_index: Option\<RowIndex>)|读取后增加加行索引号，起始索引为0. RowIndex{name:"RowIndex",offset:0}
with_n_rows(num_rows: Option\<usize>)|只读取n行，在多线程条件下无法保证精确n。
with_schema_overwrite( self, schema_overwrite)|设置部分字段的类型
finish()|获得最终的lazyframe
