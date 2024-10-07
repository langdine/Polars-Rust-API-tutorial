# Series常用API

方法|含义
--|--
series.len()|返回Series长度
series.name()|返回name
series.rename(&str)|重命名name
series.rechunk()|Series本质上是分块后的数组，这有助于并行运算。但经过多次索引操作后，分块会非常碎片化，这影响计算效率。rechunk将相邻chunk合并，以提升运算效率。
series.cast(&DataType::Int64)|转换Series内部数据类型，返回新series
series.chunks()|返回chunks
series.chunk_lengths|返回chunks长度迭代器
series.get(u32)|用AnyValue类型返回指定索引的值。
