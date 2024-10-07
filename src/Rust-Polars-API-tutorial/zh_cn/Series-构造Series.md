# 构造Series

Series是储存字段的数据结构。其中保存了字段内的所有元素，所有元素要求类型相同。Series的本质是ChunkedArray，即分块存储的数组。在Polars中，Series里的数据是以块（chunks）的形式存储的，每个块都是一个独立的数组。这种设计可以提高数据处理的效率，特别是在进行大规模数据操作时，分块后的数据可以并行运算。

```rust
use polars::prelude::*;
let s = Series::new("字段名", vec![0i32, 2, 1,3, 8]);
```
