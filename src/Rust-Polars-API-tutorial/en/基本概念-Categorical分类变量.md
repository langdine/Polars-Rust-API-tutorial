# Categorical variables

Categorical variables are used to optimize string handling. When data is stored, there are many cases where strings are used to represent categories, such as city, gender, ethnicity, species, and so on. However, after loading into memory, a large number of duplicate strings take up unnecessary resources, and string comparison operations are also very time-consuming. Categorical variables are used to solve the above problems:

String column| Categorical columns|-
--|--|--
Series| Type ID| Type comparison
polar bear|0| polar bear
panda|1| panda
brown bear|2| brown bear
panda|1|
brown bear|2|
brown bear|2|
polar bear|0|
…|…|

After the conversion of the string column -> Categorical, we only need to store the type ID and the type comparison table. This saves a lot of memory and also speeds up == operations. The benefit of this encoding is that the string value is stored only once. In addition, when we perform operations such as sorting, counting, we can compare IDs, which is much faster than processing string data. Polars supports processing categorical data with two different data types: Enum and Categorical. When an Enum is used, the  number of class of Enum (Note: Not items of Series) is fixed, an element that does not belong to the Enum is considered to be a data error. The number of categories is not fixed, then with Categorical, new types will be silently added to Categorical. If your needs change in the process, you can always switch from one to the other.

There are two types of sorting of categorical variables, one is based on the numerical order of type IDs, and the other is based on string order. The difference is specified when building the type:

```rust
DataType::Categorical(None, CategoricalOrdering::Physical) //ID order

DataType::Categorical(None, CategoricalOrdering::Lexical) //Lexical order
```

Exp：

```rust
//Creating a Series of type Categorical
let s= Series::new("字段名", vec!["option1","option2","option3","option4"])
          .cast(DataType::Categorical(None, CategoricalOrdering::Physical));

```
