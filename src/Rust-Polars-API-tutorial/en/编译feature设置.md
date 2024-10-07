# Compile feature settings

```toml
#cargo.toml recommended dependencies, Polars supports more features that will result in slower compilation.
[dependencies]
polars = {version="0.43.0",features=["mode","find_many","polars-io","csv","polars-ops","lazy","docs-selection","streaming","regex","temporal","is_unique","is_between","dtype-date","dtype-datetime","dtype-time","dtype-duration","dtype-categorical","rows","is_in","pivot"]}
polars-io = "0.43.0"
polars-lazy = "0.43.0"
```

A complete list of features: `https://docs.rs/crate/polars/latest/features`

Commonly used features|Meaning
--|--
lazy| Enable the lazy API
regex| Support for regular regex in col() expressions
sql| SQL queries are supported
streaming| Enable data flows, which allow you to process more data than you can in memory.
random| Generate a randomly sampled array.
timezones| Time zone support
strings| String extraction tool
object|ObjectChunked, an arbitrary data type, is supported, and it uses Any trait to handle different types.
json| Support serialization and deserialization of JSON
serde| Serialization and deserialization of the SERDE library are supported
serde-lazy| Serialization and deserialization of the SERDE library are supported
sort_multiple| Multi-column sorting is supported
rows|Create a DataFrame from a row and extract rows from a DataFrame. Activate pivot and transpose operations.
 The meaning of this sentence:|
asof_join|Supports ASOF connection[^asof_join] operation.
cross_join|Create two DataFrames of Cartesian product[^cross_join].
is_in| Check whether the value is in the series
is_between| Determine whether it is between the upper and lower bounds.
zip_with| Zip the two series.
arg_where| Returns indexes that meet the criteria
unique_counts| Unique value counting is supported
rank| Calculate the rank
interpolate| Interpolates the missing values of Series

--------

[^asof_join]: In data processing, "joining" refers to merging two or more datasets together according to some shared key or column. Typically, this connection requires that the values of the keys must match exactly. However, ASOF connection is a special type of connection that does not require the exact match of the key values, but allows the connection to be made according to the nearest key. This is especially useful when working with time series data, as you may want to connect the data to the closest point in time, rather than an exact match. For example, if you have a piece of data that contains stock prices, and each row of data has a timestamp, you may want to connect this data with another piece of data that contains economic indicators, which also has a timestamp, but the timestamps may not exactly match.

[^cross_join]: In data processing, the Cartesian product refers to all possible combinations of two datasets. For example, if you have two DataFrames, one containing rows A and B, and the other containing rows 1 and 2, the Cartesian product of these two DataFrames will contain four rows: (A, 1), (A, 2), (B, 1), (B, 2).
