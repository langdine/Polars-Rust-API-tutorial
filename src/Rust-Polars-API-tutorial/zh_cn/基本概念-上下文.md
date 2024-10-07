# 表达式

Polars 拥有一个强大的概念，称为表达式(Expr类型)。 Polars 表达式可以在各种上下文中使用，本质上是执行的是Fn(Series) -> Series。Expr将 Series 作为输入并将 Series 作为输出。因此Expr可以链式调用。

```rust
col("foo").sort().head(2)
```

上述片段表示选择列 "foo" ，然后对此列进行排序，然后取排序输出的前两个值。表达式的强大之处在于每个表达式都会产生一个新的表达式，而且它们可以链式调用或保存入变量或作为参数传递。您可以通过polars 的执行上下文来运行表达式。在这里，我们在select上下文中运行两个表达式：

```rust
df.lazy()
  .select([
  col("foo").sort(Default::default()).head(None),
  col("bar").filter(col("foo").eq(lit(1))).sum(),
])
.collect()?;
```

每个独立的Polars表达式都可以在不需要任何其他表达式的结果或者不需要与其他表达式进行交互的情况下独立运行。因此，Polars可能会把表达式分配到不同的线程或者处理器上同时执行。
Polars表达式是高度并行的。理解Polars表达式是学习Polars的关键步骤。

# 上下文

能接受表达式的函数被称作上下文，包括以下三种：

  含义 |  代码
  --|--
  选择|df.select(\[..\])
  分组聚合|df.groupby(..).agg(\[..\])
  横向堆叠(hstack) 或者增加列| df.with_columns(\[..\])