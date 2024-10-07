# Lazyframe API

Self=Lazyframe

API|含义
--|--
.to_dot(&self, optimized: bool)|返回执行计划的DOT语言[^DOT语言]表示 。
.explain(&self, optimized: bool)|返回string描述查询计划
.sort(self, by,sort_options) -> LazyFrame|排序[代码示例](#sort)
sort_by_exprs(self,by_exprs: E,sort_options) |根据表达式排序，这些表达式会在排序前计算,[代码示例](#sort_by_exprs)
reverse(self) -> LazyFrame|行逆序
rename(self, existing: I, new: J)|重命名字段；existing 和 new 是相同长度的可迭代对象，包含 old 和相应的 new 列名。重命名同时对所有现有列进行，而不是迭代进行。调用 rename 时，existing 中的所有列必须已存在于 LazyFrame 中。<br>`let res=lf.rename(["姓名","员工ID","值"],["name","ID","value"]).collect()?;`
drop (self, columns: I)|强行删除指定列。<br>`let res=lf.drop (["姓名","员工ID"]).collect()?;`
drop_no_validate(self, columns: I)|告诉执行计划哪些列可以删除，执行计划没有使用的列会被删除。
collect(self)|执行所有操作，把结果搜集到dataframe
profile(self)|对lazyframe做性能分析
sink_csv( self, path, CsvWriterOptions)|将结果流式写入csv文件，这适用于数据量非常大不便于写入内存时。
filter(self, predicate: E)|predicate为某个返回bool数组的表达式，值为false的行将被丢弃
slice(self, offset: i64, len: u32)|返回lazyframe，其中包含切片指定的行,offset为负数表示从末尾开始计数。lf.slice(-5,3) 表示从倒数第5个元素开始连续选择3个元素。
first(self)|第一行，等价于lf.slice(0,1) 
last(self) |最后一行，等价于lf.slice(-1,1)
tail(self,n:u32)|最后n行，等价于self.slice(-(n as i64), n)
limit(self,n:u32)|前几行，self.slice(0, n)
select(self,exprs:E)|返回表达式指定字段。见 [LazyFrame列索引](Lazyframe操作-索引LazyFrame.md#列索引)
group_by(self,by:E)|根据表达式指定的列，分组。group_by和agg函数联用。由于group算法的内部机制问题，经过group_by操作可能导致行顺序发生改变。
group_by_stable(self,by:E)|类似group_by，但是保留原有行顺序。
cross_join(self, other, suffix:)|笛卡尔联接[^cross_join]。
left_join( self,other，left_on:E,right_on: E)|self和other完成left_join,分别用left_on和right_on指定的字段作为联接键。<br>`lf.left_join(other, col("foo"), col("bar").cast(DataType::String))`
inner_join( self,other，left_on:E,right_on: E)|self和other完成inner_join,分别用left_on和right_on指定的字段作为联接键。
full_join( self,other，left_on:E,right_on: E)|self和other完成left_join,分别用left_on和right_on指定的字段作为联接键。
with_columns(self, exprs: E)|新增或删除列。
explode (self, columns: E)|对columns指定的列扩展list。
null_count()|返回lazyframe，其中每个字段包含对应字段的null计数。
unique (self,  subset, keep_strategy)|排除重复行，不能保证原行顺序
unique_stable (self,  subset, keep_strategy)|类似unique，但能保证原行顺序<br>`let mut df2=lf.unique_stable(Some(&["Element".into(),"id".into()]), uniqueKeepStrategy::First).collect()?;`
drop_nulls(self, subset: Option\<Vec\<Expr>>)|删除选择字段中存在null 的行。如果subset为None那么选择所有字段。
with_row_index(self, name: S, offset)|在offset处添加名为name的行索引字段。

[^cross_join]: 数据处理中，"笛卡尔积"是指两个数据集的所有可能的组合。例如，如果你有两个DataFrame，一个包含A、B两行，另一个包含1、2两行，那么这两个DataFrame的笛卡尔积将包含四行：(A, 1)，(A, 2)，(B, 1)，(B, 2)。

## 代码参考

### sort_by_exprs

```rust
//本段代码根据姓名和"地址"的长度排序
let mut sort_options= SortMultipleOptions::default()    
    .with_order_descending_multi([false,false])	//是否逆序
    .with_multithreaded(false)	//是否启用多线程，默认为true
    .with_nulls_last(true);	//Null放在末尾，默认为false；
lf.sort_by_exprs([col("姓名"),col("地址").str().len_chars()],sort_options)`
.collect()?;
```

### sort

```rust
let mut sort_options= SortMultipleOptions::default()
    .with_order_descending_multi([true,false])	//是否逆序
    .with_multithreaded(false)	//是否启用多线程，默认为true
    .with_nulls_last(true);	//Null放在末尾，默认为false
lf.sort(["col1","col2"],sort_options)
```

[^DOT语言]: DOT语言是一种用于描述图形的文本格式，通常用于表示图的结构。它是Graphviz工具的一部分，广泛应用于可视化图形数据。在Polars中，获取逻辑计划的DOT语言表示意味着将查询的逻辑结构转换为DOT格式，这样可以使用Graphviz等工具可视化查询执行计划。这种可视化帮助开发者理解查询的执行过程及其优化。
