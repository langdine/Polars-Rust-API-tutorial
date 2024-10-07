# 索引Series

## series.slice(idx,len)切片索引

返回的Series是self的视图，并不拷贝数据。 如果_offset为负数，那么从数组末尾开始计数。
函数声明：
`fn slice(&self, _offset: i64, _length: usize) -> Series`


示例：

```rust
let s = Series::new("字段名", [0i32, 1, 8]);
let s2 = s.slice(2, 4);
```

## 索引号索引

### `series.take_slice(&[u32])`

当索引数组保存在切片里，使用take_slice

```rust
let s = Series::new("foo".into(), [1i32 ,2, 4,5,7,3]);
let s2 = s.take_slice(&[1,3,5])
//s2 == Series[2,5,3]
```

## `series.take(&IdxCa)`

索引保存在IdxCA类型，使用take方法。IdxCa是`ChunkedArray<UInt32Type>`类型的别名。

```rust
let s = Series::new("foo".into(), [1i32 ,2, 4,5,7,3]);
let idx0:IdxCa= IdxCa::from_vec("索引".into(), vec![1,3,4]);
let res0 = s.take(&idx0).unwrap();
println!("{}", res0); 
```

## 逻辑索引

`series.filter(bool_idxs)`

bool_idx为逻辑索引，本质上是一系列bool值的数组。 bool_idx值为true的元素所对应的索引，将从series复制，并返回。这里潜在的要求是len(bool_idx)==len(series)。bool_dix是BooleanChunked类型，是ChunkedArray类型的别名，
series定义了bool运算函数，支持Series和单个值比较，也支持Series之间比较。

```rust
        let s1 = Series::new("字段名1", vec![0i32, 1,2,5,8]);
        let s2 = Series::new("字段名2", vec![2i32, -1,3,-5,8]);
        let idx = s1.gt(&s2).unwrap() & s1.lt_eq(5).unwrap();
        let s2=s.filter(&idx).unwrap();
```

## 逻辑运算

`series.filter(bool_idxs)`中的bool_idxs是BooleanChunked类型，可由逻辑运算返回。

Series比较方法|含义
--|--
.gt(&Series)|>
.gt_eq(&Series)|>=
.equal()|==
.equal_missing()|==
.not_equal()|!=
.not_equal_missing()|!=
.lt()|<
.lt_eq()|<=
.is_nan()|是否为NaN
.is_not_nan()|不是NaN
.is_empty()|是否为空, series.len()==0
.is_finite()|是否无穷
.is_infinite()|是否非无穷
.none_to_nan(&self)|将缺失值转换成NaN
.is_null()|是否null，表示缺失值
.Is_not_null()|不是null

*_missing系列函数用于比较两个Series或ChunkedArray是否相等，同时考虑到可能存在的缺失值（也就是None或NaN）。 在很多情况下，直接比较两个包含缺失值的Series或ChunkedArray可能会得到不准确的结果，因为None或NaN并不等于任何值，包括它们自身。equal_missing函数提供了一种方式来处理这种情况，它将两个相同位置都是缺失值的元素视为相等。
