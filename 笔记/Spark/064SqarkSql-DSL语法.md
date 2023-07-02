1. 查看 DataFrame 的 Schema 信息

   ```sh
   df.printSchema
   ```

2. 只查看"username"列数据

   ```sh
   df.select("username").show()
   ```

3. age+1

   ```sh
   df.select($"username",$"age"+1).show
   #或者
   df.select('username,'age+1).show
   ```

4. 别名

   ```sh
   df.select('username,'age+1 as "ageAdd").show
   +--------+------+
   |username|ageAdd|
   +--------+------+
   |  yknife|    11|
   |     leo|    31|
   +--------+------+
   ```

5. 过滤年龄大于20

   ```sh
   df.filter('age > 20).show
   ```

6. 根据年龄分组计数

   ```sh
   df.groupBy("age").count().show
   +---+-----+
   |age|count|
   +---+-----+
   | 10|    1|
   | 30|    1|
   +---+-----+
   ```

   