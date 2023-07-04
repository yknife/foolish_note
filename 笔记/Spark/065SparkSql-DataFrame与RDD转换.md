### 创建DataFrame

```sh
# 方式一：从文件中读取
val df = spark.read.json("data/json/user.json")
# 方式二：使用RDD转换
val df = sc.makeRDD(List(("zh1",31),("zh2",32))).toDF("username","age")
```

### RDD转DateFrame

```sh
val rdd = sc.makeRDD(List(1,2,3,4))
rdd.toDF("id").show
+---+
| id|
+---+
|  1|
|  2|
|  3|
|  4|
+---+
#实际开发中，一般通过样例类将 RDD 转换为 DataFrame
case class User(name:String,age:Int)
sc.makeRDD(List(User("yknife",30),User("leo",20))).toDF.show
```

### DateFrame转RDD

```sh
# 一步无聊的操作，为了体现DataFrame转RDD的效果
val arr = sc.makeRDD(List(User("yknife",30),User("leo",20))).toDF.rdd.collect
#Array[org.apache.spark.sql.Row] = Array([yknife,30], [leo,20])
arr(0)
#org.apache.spark.sql.Row = [yknife,30]
arr(0)(0)
#Any = yknife
arr(0).getAs[String]("name")
#String = yknife
```

