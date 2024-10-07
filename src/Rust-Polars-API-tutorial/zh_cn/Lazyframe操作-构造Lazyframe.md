# 构造Lazyframe

构造lazyframe可以通过[IO章节](IO.md)介绍的API从文件中载入。也可以调用dataframe的lazy()方法可以生成对应的lazyframe。

```rust
let lf=df.lazy();
```
