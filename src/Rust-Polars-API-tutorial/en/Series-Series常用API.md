# Common Series API

Method | Description
--|--
`series.len()` | Returns the length of the Series.
`series.name()` | Returns the name of the Series.
`series.rename(&str)` | Renames the Series.
`series.rechunk()` | A Series is essentially an array divided into chunks, which aids in parallel computation. However, after multiple indexing operations, the chunks can become fragmented, affecting computational efficiency. `rechunk` merges adjacent chunks to improve efficiency.
`series.cast(&DataType::Int64)` | Converts the internal data type of the Series and returns a new Series.
`series.chunks()` | Returns the chunks of the Series.
`series.chunk_lengths` | Returns an iterator over the lengths of the chunks.
`series.get(u32)` | Returns the value at the specified index as an `AnyValue`.