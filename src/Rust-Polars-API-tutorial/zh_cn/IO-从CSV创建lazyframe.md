# 从CSV创建lazyframe

用于lazy方式读取csv文件，普通读取文件的方式会立刻将数据载入内存。对于csv文件非常大来说会占用不必要的资源。Lazy API将事实读取推迟到lazyDataframe的collect()方法调用。在collect()函数调用之前，你可以设置处理方式和计算表达式。

```rust
//需要polars-io特性，从csv文件创建，lazyframe
use polars::prelude::*;
let lazyreader=LazyCsvReader::new("./test.csv");
let lazyreader=LazyCsvReader::new_paths(&["./test0.csv","./test1.csv"]); 读取多个文件。
let lf= lazyreader.finish()?;
```

## LazyCsvReader API

API|含义
--|--
with_glob(toggle: bool)|对路径开启glob通配符功能。
with_skip_rows_after_header(self, offset: usize)|在header后跳过offset数量的行
with_row_index(self, row_index: Option\<RowIndex>)|读取后增加加行索引号，起始索引为0. RowIndex{name:"RowIndex",offset:0}
with_n_rows(num_rows: Option\<usize>)|只读取n行，在多线程条件下无法保证精确n。
with_skip_rows( n: usize)|跳过n行，header从行索引n开始。
with_has_header(has_header: bool)|是否存在标题行
with_separator(separator: u8)|默认字段分隔符
with_comment_prefix(comment_prefix: Option\<&str>)|注释标记，以comment_prefix开始的行被当做注释忽略。
with_quote_char(quote_char: Option\<u8>)|字符串引号标记，b'"'
with_eol_char(eol_char: u8)|行结束符，b'\n'
with_null_values(null_values: Option\<NullValues>)|设置识别为空值的字符串
with_encoding(CsvEncoding::Utf8)|设置字符集
finish()|获得最终的lazyframe
