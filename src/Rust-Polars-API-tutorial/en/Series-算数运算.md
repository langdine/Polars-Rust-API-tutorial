# Arithmetic Operations

Polars defines arithmetic operations such as addition, subtraction, multiplication, and division. Here's how you can use them:

```rust
let s = Series::new("a", [1, 2, 3]);
let out_add = &s + &s;
let out_sub = &s - &s;
let out_div = &s / &s;
let out_mul = &s * &s;

// Supports operations between Series and Series, as well as Series and scalar values
let s: Series = (1..3).collect();
let out_add_one = &s + 1;
let out_multiply = &s * 10;
let out_divide = 1.div(&s); // Division
let out_add = 1.add(&s);    // Addition
let out_subtract = 1.sub(&s); // Subtraction
let out_multiply = 1.mul(&s); // Multiplication
```

In this example, you can see how to perform arithmetic operations both between two `Series` and between a `Series` and a scalar value.