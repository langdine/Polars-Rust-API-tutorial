# 算数运算

Polars定义了 加减乘除等算数运算，用法如下。

```rust
let s = Series::new("a", [1 , 2, 3]);
let out_add = &s + &s;
let out_sub = &s - &s;
let out_div = &s / &s;
let out_mul = &s * &s;
//支持Series 和 Series运算，也支持Series和数的运算
let s: Series = (1..3).collect();
let out_add_one = &s + 1;
let out_multiply = &s * 10;
let out_divide = 1.div(&s);//除法
let out_add = 1.add(&s);  //加法
let out_subtract = 1.sub(&s); //减法
let out_multiply = 1.mul(&s);//乘法
```
