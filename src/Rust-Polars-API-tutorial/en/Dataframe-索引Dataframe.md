# Indexing a DataFrame

## Column Indexing

```rust
let res: &Series = employee_df.column("Employee")?;  // Returns a reference to a Series based on a single column name
let res: &Series = employee_df.select_at_idx(1)?;  // Returns a reference to a Series based on an index
let res: DataFrame = employee_df.select(["Category", "Salary"])?; // Copies the specified fields and returns a new DataFrame
let sv: Vec<&Series> = employee_df.columns(["Category", "Salary"])?; // Returns references to Series based on multiple column names
let res: &[Series] = employee_df.get_columns()?; // Returns a slice of all columns, pointing to the internal data structure of the DataFrame without copying data
let res: Vec<Series> = employee_df.take_columns(); // Takes ownership of all columns
```

## Row Indexing

### Boolean Indexing

```rust
let se = employee_df.column("Performance")?;
let mask = se.gt_eq(80)?; // Checks if se is greater than or equal to 80. Returns a Boolean array.
let res: DataFrame = employee_df.filter(&mask)?;
```

### Slice Indexing

```rust
let res: DataFrame = employee_df.slice(5, 3);
```

### Index Values

```rust
let idx = IdxCa::new("idx".into(), [0, 1, 9]);
let res: DataFrame = employee_df.take(&idx)?; // Returns rows with indices 0, 1, and 9
```
