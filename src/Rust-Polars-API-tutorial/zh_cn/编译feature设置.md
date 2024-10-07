# 编译feature设置

```toml
#cargo.toml推荐依赖，Polars支持更多的特性，会导致编译速度减慢。
[dependencies]
polars = {version="0.43.0",features=["mode","find_many","polars-io","csv","polars-ops","lazy","docs-selection","streaming","regex","temporal","is_unique","is_between","dtype-date","dtype-datetime","dtype-time","dtype-duration","dtype-categorical","rows","is_in","pivot"]}
polars-io = "0.43.0"
polars-lazy = "0.43.0"
```

完整的编译特性列表：https://docs.rs/crate/polars/latest/features

常用特性|含义
--|--
lazy|启用lazy API
regex|在col()表达式中支持正则
sql|支持SQL查询
streaming|启用数据流，这支持处理数据量大于内存。
random|生成随机采样的array。
timezones|时区支持
strings|字符串提取工具
object|支持任意数据类型ObjectChunked<T>，它利用Any trait处理不同类型。
json|支持json的序列化和反序列化
serde|支持serde库序列化和反序列化
serde-lazy|支持serde库序列化和反序列化
sort_multiple|支持多列排序
rows|从行中创建 DataFrame 并从 DataFrame 中提取行。激活数据透视和转置操作。
这句话的意思是
asof_join|支持ASOF连接[^asof_join]操作。
cross_join|创建两个DataFrame的笛卡尔积[^cross_join] 。
is_in|判断值是否在Series中
is_between|判断是否在上下限之间。
zip_with|将两个Series进行Zip运算。
arg_where|返回满足条件的索引
unique_counts|支持唯一值计数
rank|计算秩
interpolate|对Series的缺失值进行插值

--------
[^asof_join]: 在数据处理中，"连接"是指将两个或更多的数据集根据一些共享的键（key）或列合并在一起。通常，这种连接要求键的值必须完全匹配。然而，ASOF连接是一种特殊的连接方式，它不要求键的值完全匹配，而是允许根据最接近的键进行连接。这在处理时间序列数据时特别有用，因为你可能想要将数据连接到最接近的时间点，而不是精确匹配的时间点。例如，如果你有一份包含股票价格的数据，每行数据都有一个时间戳，你可能想要将这份数据与另一份包含经济指标的数据进行连接，这份数据也有时间戳，但时间戳可能并不完全匹配。

[^cross_join]: 数据处理中，"笛卡尔积"是指两个数据集的所有可能的组合。例如，如果你有两个DataFrame，一个包含A、B两行，另一个包含1、2两行，那么这两个DataFrame的笛卡尔积将包含四行：(A, 1)，(A, 2)，(B, 1)，(B, 2)。
