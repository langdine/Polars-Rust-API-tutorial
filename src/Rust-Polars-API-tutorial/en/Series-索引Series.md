# Indexing a Series

## Slice Indexing with `series.slice(idx, len)`

The returned `Series` is a view of `self` and does not copy the data. If `_offset` is negative, it counts from the end of the array.

Function declaration:
`fn slice(&self, _offset: i64, _length: usize) -> Series`

Example:

```rust
let s = Series::new("field_name", [0i32, 1, 8]);
let s2 = s.slice(2, 4);
```

## Indexing by Position

### `series.take_slice(&[u32])`

Use `take_slice` when the index array is stored in a slice.

```rust
let s = Series::new("foo".into(), [1i32, 2, 4, 5, 7, 3]);
let s2 = s.take_slice(&[1, 3, 5]);
// s2 == Series[2, 5, 3]
```

## `series.take(&IdxCa)`

Use the `take` method when indices are stored in the `IdxCa` type. `IdxCa` is an alias for `ChunkedArray<UInt32Type>`.

```rust
let s = Series::new("foo".into(), [1i32, 2, 4, 5, 7, 3]);
let idx0: IdxCa = IdxCa::from_vec("index".into(), vec![1, 3, 4]);
let res0 = s.take(&idx0).unwrap();
println!("{}", res0);
```

## Logical Indexing

`series.filter(bool_idxs)`
`bool_idx` is a logical index, essentially an array of boolean values. Elements corresponding to `true` in `bool_idx` are copied from the series and returned. The implicit requirement is that `len(bool_idx) == len(series)`. `bool_idx` is of type `BooleanChunked`, an alias for `ChunkedArray`. The series defines boolean operation functions, supporting comparisons between a `Series` and a single value, as well as between two `Series`.

```rust
let s1 = Series::new("field_name1", vec![0i32, 1, 2, 5, 8]);
let s2 = Series::new("field_name2", vec![2i32, -1, 3, -5, 8]);
let idx = s1.gt(&s2).unwrap() & s1.lt_eq(5).unwrap();
let s2 = s1.filter(&idx).unwrap();
```

## Logical Operations

In `series.filter(bool_idxs)`, `bool_idxs` is of type `BooleanChunked`, which can be returned by logical operations.

Series Comparison Methods | Meaning
--|--
.gt(&Series)| >
.gt_eq(&Series)| >=
.equal()| ==
.equal_missing()| ==
.not_equal()| !=
.not_equal_missing()| !=
.lt()| <
.lt_eq()| <=
.is_nan()| Is NaN
.is_not_nan()| Is not NaN
.is_empty()| Is empty, `series.len() == 0`
.is_finite()| Is finite
.is_infinite()| Is infinite
.none_to_nan(&self)| Converts missing values to NaN
.is_null()| Is null, indicating missing values
.is_not_null()| Is not null

The `*_missing` series of functions are used to compare two `Series` or `ChunkedArray` for equality, taking into account possible missing values (i.e., `None` or `NaN`). In many cases, directly comparing two `Series` or `ChunkedArray` containing missing values may yield inaccurate results because `None` or `NaN` is not equal to any value, including themselves. The `equal_missing` function provides a way to handle this situation by treating elements with missing values at the same position as equal.