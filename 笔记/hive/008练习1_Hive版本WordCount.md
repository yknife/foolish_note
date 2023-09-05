

### 数据准备

```sql
+------------------+
| word_count.line  |
+------------------+
| hello world      |
| hello java       |
| hello hive       |
+------------------+

```

### Sql

```sql
select word,count(*) from (
 select explode(split(line,' ')) word from word_count
)t1 group by word
```

### 结果

```sql
+--------+------+
|  word  | _c1  |
+--------+------+
| hello  | 3    |
| hive   | 1    |
| java   | 1    |
| world  | 1    |
+--------+------+
```



