### Explode

EXPLODE(col)：将 hive 一列中复杂的 Array 或者 Map 结构拆分成多行。

### LATERAL VIEW

用法：LATERAL VIEW udtf(expression) tableAlias AS columnAlias

解释：用于和 split, explode 等 UDTF 一起使用，它能够将一列数据拆成多行数据，在此 基础上可以对拆分后的数据进行聚合。

### 数据准备

```sql
《疑犯追踪》	悬疑,动作,科幻,剧情
《Lie to me》	悬疑,警匪,动作,心理,剧情
《战狼 2》	战争,动作,灾难
```

### 需求

将电影分类中的数组数据展开。结果如下

```sql
《疑犯追踪》 悬疑
《疑犯追踪》 动作
《疑犯追踪》 科幻
《疑犯追踪》 剧情
《Lie to me》 悬疑
《Lie to me》 警匪
《Lie to me》 动作
《Lie to me》 心理
《Lie to me》 剧情
《战狼 2》 战争
《战狼 2》 动作
《战狼 2》 灾难
```

### Sql

```sql
-- 需要关联movie_info表中movie，需要配合lateral view使用
select movie,category_name from movie_info 
lateral view explode(split(category,",")) movie_info_tmp as category_name
-- 如果不需要关联字段可以直接使用炸裂函数，不用lateral view
select explode(split(category,",")) from movie_info 
```





