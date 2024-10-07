# Renaming with alias

## alias
`alias` is used to change the name of a single field.

```rust
let df_alias = df.clone().lazy()
.select([ (col("nrs") + lit(5)).alias("nrs + 5"),
(col("nrs") - lit(5)).alias("nrs - 5")])
.collect()?;
```

## name
`name()` returns an `ExprNameNameSpace`, a type that can operate on multiple field names.

| Method List | Meaning |
|-------------|---------|
| keep() | Uses the original field name. Even if the field name is set using `alias`, `keep` will ensure the use of the original field name. <br>`col("*").alias("foo").name().keep()` |
| map(F) | Uses a custom function to return the field name, with the input parameter being the original field name. <br> F satisfies `Fn(&PlSmallStr) -> Result<PlSmallStr, PolarsError>` |
| prefix(prefix: &str) | The new field name is the original field name with a prefix. `prefix` and `suffix` cannot be used simultaneously. In this case, consider using `map` to apply a custom function. |
| suffix(suffix: &str) | The new field name is the original field name with a suffix. `prefix` and `suffix` cannot be used simultaneously. In this case, consider using `map` to apply a custom function. |
| to_lowercase() | Converts to lowercase letters. |
| to_uppercase() | Converts to uppercase letters. |