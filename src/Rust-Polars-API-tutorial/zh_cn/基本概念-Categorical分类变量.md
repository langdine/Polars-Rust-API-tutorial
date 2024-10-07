# Categorical分类变量

分类变量用于优化字符串处理。数据存储时，大量存在用字符串表示类别的情况，例如城市、性别、民族、物种等等。但加载进内存后，大量重复的字符串占用不必要的资源，并且字符串比较运算也非常耗时。分类变量用于解决以上问题：

字符串列|分类列|-
--|--|--
Series|类型ID|类型对照
北极熊|0|北极熊
熊猫|1|熊猫
棕熊|2|棕熊
熊猫|1|
棕熊|2|
棕熊|2|
北极熊|0|
…|…|

经过字符串列 -\> Categorical的转换，我们仅仅需要存储类型ID和类型对照表。这大量的节省了内存，也加速了==运算。这种编码的好处是只存储一次字符串值。此外，当我们执行操作（例如排序，计数）时，我们可以对ID进行比较，这比处理字符串数据要快得多。Polars
支持使用两种不同的数据类型处理分类数据： Enum 和 Categorical
。当类别数量固定时使用Enum，出现不属于Enum的元素时会被认为出现数据错误。当类别的数量不是固定的，那么使用
Categorical，出现新的类型会默默的添加进Categorical。如果你的需求在过程中发生变化，你总是可以从一个转换为另一个。

分类变量的排序有两种类型，一种是依据类型ID数值顺序，一种是根据字符串顺序。这个区别是构建类型时指定：

```rust
DataType::Categorical(None, CategoricalOrdering::Physical) //对照表ID顺序

DataType::Categorical(None, CategoricalOrdering::Lexical) //词法顺序
```

示例：

```rust
//生成一个Categorical类型Series
let s= Series::new("字段名", vec!["option1","option2","option3","option4"])
          .cast(DataType::Categorical(None, CategoricalOrdering::Physical));

```
