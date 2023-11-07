## index

### what is index?

- Indexes are used to find rows with specific column values quickly. 
- Without an index, MySQL must begin with the first row and then read through the entire table to find the relevant rows. 
- The larger the table, the more this costs. 
- If the table has an index for the columns in question, MySQL can quickly determine the position to seek to in the middle of the data file without having to look at all the data. 
- This is much faster than reading every row sequentially.

### MySQL uses indexes for these operations

- To find the rows matching a WHERE clause quickly.
- To eliminate rows from consideration.
- If the table has a multiple-column index, any leftmost prefix of the index can be used by the optimizer to look up rows.
- To retrieve rows from other tables when performing joins. 
- To find the MIN() or MAX() value for a specific indexed column key_col.
- To sort or group a table if the sorting or grouping is done on a leftmost prefix of a usable index