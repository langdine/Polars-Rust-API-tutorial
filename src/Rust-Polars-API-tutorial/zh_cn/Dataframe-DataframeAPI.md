# Dataframe API

API|含义
--|--
df.get_column_index(name: &str) -> Option\<usize>|根据name，返回Series对应的索引
df.column(name: &str) -> Result<&Series, PolarsError>|根据列名返回Series引用
df.select_at_idx(idx: usize) -> Option<&Series>|根据索引返回对应Series的引用
df.select_by_range(range: R) -> Result<DataFrame, PolarsError>|根据Range返回新的dataframe
df.columns(&self, names: I) -> Result<Vec<&Series>, PolarsError>|let sv: Vec<&Series> = df.columns(["类别", "薪水"])?; 返回Vec<&Series>
df.select(&self, selection: I) -> Result<DataFrame, PolarsError>|df.select(["类别", "薪水"])返回新的DataFrame
df.get_columns() -> &[Series]|返回所有列的切片
df.take_columns() -> Vec\<Series>|获得所有列的所有权
df.get_column_names() -> Vec<&PlSmallStr>|所有列名的引用
df.get_column_names_owned(&self) -> Vec\<PlSmallStr>|clone并返回所有列名，所有权。
df.set_column_names (names: I)|设置列名
df.dtypes() -> Vec\<DataType>|返回每个字段的类型
df.filter(mask: &ChunkedArray\<BooleanType>) -> Result<DataFrame, PolarsError>|mask的每一个元素代表一行，筛选出mask==true的行。
df.take(indices: &ChunkedArray\<UInt32Type>) -> Result<DataFrame, PolarsError>|返回指定索引的行。    let idx = IdxCa::new("idx".into(), [0, 1, 9]); df.take(&idx)
df.slice(offset: i64, length: usize) -> DataFrame|返回切片指定的行。
df.rename(oldname:&str,newname:PlSmallStr) -> Result<&mut DataFrame, PolarsError>|重命名字段，df.rename("oldname", "newname".into())
df.sort_in_place(by: impl IntoVec\<PlSmallStr>,sort_options) -> Result<&mut DataFrame, PolarsError>|就地排序df，df就保存了排序结果，df.sort(["col1","col2"], Default::default())
df.sort (by: impl IntoVec\<PlSmallStr>,sort_options) -> Result<DataFrame, PolarsError>|排序df，返回排序后新的Dataframe，原变量不变。
df.replace(column: &str,new_col: S) -> Result<&mut DataFrame, PolarsError>|替换指定column为新的Series。传入的new_col的name会被赋值为指定的名称
df.with_column(new_col: IntoSeries)|df中添加一列，如果new_col的name已经存在，那么覆盖旧值
df.insert_column(index: usize,column: S)|在指定索引处插入column
df.hstack_mut(columns: &[Series])|df中添加多列，改变df
df.hstack(columns: &[Series])|df中添加多列，返回新的dataframe
df.get(idx) -> Option<Vec<AnyValue<'_>>>|返回指定行。效率低下
df.with_row_index_mut (name: PlSmallStr, offset: Option<u32>) -> -> &mut DataFrame|在指定offset索引位置添加索引列，列名name。
df.schema() -> Schema\<DataType>|获取Dataframe的结构，包含字段名和字段类型。
df.fields() -> Vec\<Field>|返回字段信息
df. estimated_size()|获取堆内存占用，字节为单位。
df.explode(columns: I) -> Result<DataFrame, PolarsError>|将list Series解包。见 [字符串分割到多行](表达式-字符串操作.md#字符串分割到多行)
df.unnest(cols)|解包struct到多列. See [字符串分割到多列](表达式-字符串操作.md#分割到多列)
df.group_by(["col1","col2"…])|分组
df.iter()|创建按列迭代器
df.shape()|返回(height, width)
df.height()|返回高度
df.width()|返回宽度
df.clear()|清空dataframe
df.is_empty()->Dataframe是否为空
df.vstack(&self, other: &DataFrame)|将两个dataframe对应字段连接到一起，返回新的dataframe。other和df的字段顺序、类型、列名必须严格一致。建议在完成vstack操作后调用DataFrame::align_chunks。
df.vstack_mut() 同vstack，vstack_mut修改df本身，而不是返回新dataframe。
df.pop()|弹出最后一个字段，并返回被弹出的Series
df.drop_in_place(name: &str)|弹出指定字段并返回被弹出的Series
df.drop(name: &str)|返回一个新的dataframe，其中指定的字段被删除。
df.drop_many(names: I)|删除多个字段，返回一个新的dataframe，其中指定的字段被删除。
df.split_at(offset: i64) -> (DataFrame, DataFrame)|指定行索引处分割
df.head(length: Option\<usize>)|返回新的dataframe，包含df的头length行。
df.tail(length: Option\<usize>)|返回新的dataframe，包含df的最后length行。
df.unique|清除重复行，无法保留原顺序
df.unique_stable|清除重复行，保证原顺序，
let mut df2=df1.unique_stable(Some(&["Element".into(),"id".into()]), UniqueKeepStrategy::First, None)?;
df.unique (None, UniqueKeepStrategy::First, None)?
df.is_unique
df.is_duplicated
df.null_count()|返回新dataframe其中每个字段包含df对应字段的null值计数
