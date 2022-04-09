```sql
select ROW_NUMBER() over (order by id) row_num,name from t_user
```

使用场景：分页查询有时候需要临时指定某个index，然后定位到这条数据，并在这个位置前后进行上下文查询，此时需要根据row_number对查询的各个结果进行一个定位，某个元素多次查询时的位置应该不变。