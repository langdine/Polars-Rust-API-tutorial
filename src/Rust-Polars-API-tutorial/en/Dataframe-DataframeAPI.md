# DataFrame API

API | Description
-- | --
df.get_column_index(name: &str) -> Option\<usize> | Returns the index of the Series corresponding to the name
df.column(name: &str) -> Result<&Series, PolarsError> | Returns a reference to the Series based on the column name
df.select_at_idx(idx: usize) -> Option<&Series> | Returns a reference to the Series based on the index
df.select_by_range(range: R) -> Result<DataFrame, PolarsError> | Returns a new DataFrame based on the Range
df.columns(&self, names: I) -> Result<Vec<&Series>, PolarsError> | let sv: Vec<&Series> = df.columns(["Category", "Salary"])?; Returns Vec<&Series>
df.select(&self, selection: I) -> Result<DataFrame, PolarsError> | df.select(["Category", "Salary"]) returns a new DataFrame
df.get_columns() -> &[Series] | Returns a slice of all columns
df.take_columns() -> Vec\<Series> | Takes ownership of all columns
df.get_column_names() -> Vec<&PlSmallStr> | References to all column names
df.get_column_names_owned(&self) -> Vec\<PlSmallStr> | Clones and returns all column names, with ownership
df.set_column_names(names: I) | Sets column names
df.dtypes() -> Vec\<DataType> | Returns the type of each field
df.filter(mask: &ChunkedArray\<BooleanType>) -> Result<DataFrame, PolarsError> | Each element of the mask represents a row, filtering out rows where mask == true
df.take(indices: &ChunkedArray\<UInt32Type>) -> Result<DataFrame, PolarsError> | Returns rows with specified indices. let idx = IdxCa::new("idx".into(), [0, 1, 9]); df.take(&idx)
df.slice(offset: i64, length: usize) -> DataFrame | Returns rows specified by the slice
df.rename(oldname: &str, newname: PlSmallStr) -> Result<&mut DataFrame, PolarsError> | Renames a field, df.rename("oldname", "newname".into())
df.sort_in_place(by: impl IntoVec\<PlSmallStr>, sort_options) -> Result<&mut DataFrame, PolarsError> | Sorts df in place, df retains the sorted result, df.sort(["col1", "col2"], Default::default())
df.sort(by: impl IntoVec\<PlSmallStr>, sort_options) -> Result<DataFrame, PolarsError> | Sorts df, returns a new sorted DataFrame, original variable remains unchanged
df.replace(column: &str, new_col: S) -> Result<&mut DataFrame, PolarsError> | Replaces the specified column with a new Series. The name of the new_col will be assigned to the specified name
df.with_column(new_col: IntoSeries) | Adds a column to df, if new_col's name already exists, it overwrites the old value
df.insert_column(index: usize, column: S) | Inserts a column at the specified index
df.hstack_mut(columns: &[Series]) | Adds multiple columns to df, modifying df
df.hstack(columns: &[Series]) | Adds multiple columns to df, returns a new DataFrame
df.get(idx) -> Option<Vec<AnyValue<'_>>> | Returns the specified row. Inefficient
df.with_row_index_mut(name: PlSmallStr, offset: Option<u32>) -> &mut DataFrame | Adds an index column at the specified offset index, with column name name
df.schema() -> Schema\<DataType> | Gets the structure of the DataFrame, including field names and field types
df.fields() -> Vec\<Field> | Returns field information
df.estimated_size() | Gets heap memory usage in bytes
df.explode(columns: I) -> Result<DataFrame, PolarsError> | Unpacks a list Series to lines. See [Splitting Strings into Multiple Lines](表达式-字符串操作.md#splitting-strings-into-multiple-lines), [Unpack values wrapped in a list to lines](Dataframe-分组和简单聚合.md#step-2)
df.unnest(cols)|Unpack struct into multiple columns. See [Splitting into Multiple Columns](表达式-字符串操作.md#splitting-into-multiple-columns)
df.group_by(["col1", "col2"…]) | Groups by specified columns
df.iter() | Creates an iterator by columns
df.shape() | Returns (height, width)
df.height() | Returns the height
df.width() | Returns the width
df.clear() | Clears the DataFrame
df.is_empty() | Checks if the DataFrame is empty
df.vstack(&self, other: &DataFrame) | Concatenates corresponding fields of two DataFrames, returning a new DataFrame. The field order, type, and column names of other and df must match exactly. It is recommended to call DataFrame::align_chunks after completing the vstack operation.
df.vstack_mut() | Same as vstack, but vstack_mut modifies df itself instead of returning a new DataFrame
df.pop() | Pops the last field and returns the popped Series
df.drop_in_place(name: &str) | Pops the specified field and returns the popped Series
df.drop(name: &str) | Returns a new DataFrame with the specified field removed
df.drop_many(names: I) | Deletes multiple fields, returning a new DataFrame with the specified fields removed
df.split_at(offset: i64) -> (DataFrame, DataFrame) | Splits at the specified row index
df.head(length: Option\<usize>) | Returns a new DataFrame containing the first length rows of df
df.tail(length: Option\<usize>) | Returns a new DataFrame containing the last length rows of df
df.unique | Removes duplicate rows, cannot retain original order
df.unique_stable | Removes duplicate rows, retains original order
let mut df2 = df1.unique_stable(Some(&["Element".into(), "id".into()]), UniqueKeepStrategy::First, None)?;
df.unique(None, UniqueKeepStrategy::First, None)?
df.is_unique | Checks if rows are unique
df.is_duplicated | Checks for duplicated rows
df.null_count() | Returns a new DataFrame where each field contains the null value count of the corresponding field in df