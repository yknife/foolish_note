### 内嵌Hive

数据准备,id.txt

```sh
1
2
3
4
```

```sh
spark.sql("create table student(id int)")
spark.sql("show tables").show()
+--------+---------+-----------+
|database|tableName|isTemporary|
+--------+---------+-----------+
| default|  student|      false|
|        |     user|       true|
+--------+---------+-----------+
# hive导入本地数据到表中
spark.sql("load data local inpath 'data/id.txt' into table student")
spark.sql("select * from student").show
+---+
| id|
+---+
|  1|
|  2|
|  3|
|  4|
+---+
```

