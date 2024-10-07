# Lazyframe API

Self=Lazyframe

API|Meaning
--|--
.to_dot(&self, optimized: bool)|Returns the DOT language[^DOTlanguage] representation of the execution plan.
.explain(&self, optimized: bool)|Returns a string description of the query plan.
.sort(self, by, sort_options) -> LazyFrame|Sorts [Code Example](#sort)
sort_by_exprs(self, by_exprs: E, sort_options) |Sorts based on expressions, which are evaluated before sorting, [Code Example](#sort_by_exprs)
reverse(self) -> LazyFrame|Reverses the order of rows.
rename(self, existing: I, new: J)|Renames fields; `existing` and `new` are iterables of the same length containing old and corresponding new column names. Renaming is done for all existing columns at once, not iteratively. When calling rename, all columns in `existing` must already exist in the LazyFrame.<br>`let res=lf.rename(["姓名","员工ID","值"],["name","ID","value"]).collect()?;`
drop (self, columns: I)|Forcibly drops specified columns.<br>`let res=lf.drop (["姓名","员工ID"]).collect()?;`
drop_no_validate(self, columns: I)|Informs the execution plan which columns can be dropped; columns not used by the execution plan will be removed.
collect(self)|Executes all operations and collects the result into a dataframe.
profile(self)|Performs performance analysis on the lazyframe.
sink_csv(self, path, CsvWriterOptions)|Streams the result into a CSV file, suitable for very large data that is inconvenient to write into memory.
filter(self, predicate: E)|`predicate` is an expression returning a boolean array; rows with a false value will be discarded.
slice(self, offset: i64, len: u32)|Returns a lazyframe containing rows specified by the slice; a negative offset counts from the end. `lf.slice(-5,3)` selects 3 elements starting from the 5th last element.
first(self)|First row, equivalent to `lf.slice(0,1)`
last(self) |Last row, equivalent to `lf.slice(-1,1)`
tail(self, n: u32)|Last n rows, equivalent to `self.slice(-(n as i64), n)`
limit(self, n: u32)|First few rows, `self.slice(0, n)`
select(self, exprs: E)|Returns fields specified by expressions. See  [Expressions - column selection](Lazyframe操作-索引LazyFrame.md#column-indexing).
group_by(self, by: E)|Groups by columns specified by expressions. `group_by` is used in conjunction with the `agg` function. Due to the internal mechanism of the group algorithm, the row order may change after a `group_by` operation.
group_by_stable(self, by: E)|Similar to `group_by`, but retains the original row order.
cross_join(self, other, suffix:)|Cartesian join[^cross_join].
left_join(self, other, left_on: E, right_on: E)|Performs a left join between `self` and `other`, using fields specified by `left_on` and `right_on` as join keys.<br>`lf.left_join(other, col("foo"), col("bar").cast(DataType::String))`
inner_join(self, other, left_on: E, right_on: E)|Performs an inner join between `self` and `other`, using fields specified by `left_on` and `right_on` as join keys.
full_join(self, other, left_on: E, right_on: E)|Performs a full join between `self` and `other`, using fields specified by `left_on` and `right_on` as join keys.
with_columns(self, exprs: E)|Adds or removes columns.
explode(self, columns: E)|Expands lists in the specified columns.
null_count()|Returns a lazyframe where each field contains the null count for the corresponding field.
unique(self, subset, keep_strategy)|Excludes duplicate rows, does not guarantee original row order.
unique_stable(self, subset, keep_strategy)|Similar to `unique`, but guarantees original row order.<br>`let mut df2=lf.unique_stable(Some(&["Element".into(),"id".into()]), uniqueKeepStrategy::First).collect()?;`
drop_nulls(self, subset: Option\<Vec\<Expr>>)|Removes rows with nulls in the selected fields. If `subset` is `None`, all fields are selected.
with_row_index(self, name: S, offset)|Adds a row index field named `name` at `offset`.

[^cross_join]: In data processing, the 'Cartesian product' refers to all possible combinations of two datasets. For example, if you have two DataFrames, one containing rows A and B, and the other containing rows 1 and 2, then the Cartesian product of these two DataFrames will include four rows: (A, 1), (A, 2), (B, 1), (B, 2).
## Code Reference

### sort_by_exprs

```rust
// This code sorts based on name and the length of "address"
let mut sort_options= SortMultipleOptions::default()    
    .with_order_descending_multi([false,false]) // Whether to sort in descending order
    .with_multithreaded(false)  // Whether to enable multithreading, default is true
    .with_nulls_last(true); // Place nulls at the end, default is false;
lf.sort_by_exprs([col("name"),col("addr").str().len_chars()],sort_options)`
.collect()?;
```

### sort

```rust
let mut sort_options= SortMultipleOptions::default()
    .with_order_descending_multi([true,false])  // Whether to sort in descending order
    .with_multithreaded(false)  // Whether to enable multithreading, default is true
    .with_nulls_last(true); // Place nulls at the end, default is false
lf.sort(["col1","col2"],sort_options)
```

[^DOTlanguage]: DOT language is a text format used to describe graphs, commonly used to represent the structure of graphs. It is part of the Graphviz tool and is widely used for visualizing graphical data. In Polars, obtaining the DOT language representation of a logical plan means converting the logical structure of a query into DOT format, which can then be visualized using tools like Graphviz. This visualization helps developers understand the execution process and optimization of queries.