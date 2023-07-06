## MySql

### 导入依赖

```xml
<dependency>
 <groupId>mysql</groupId>
 <artifactId>mysql-connector-java</artifactId>
 <version>5.1.27</version>
</dependency>
```

```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlMysql")
  val spark = SparkSession.builder().config(sparkConf).getOrCreate()
  //从一张表读取数据
  val df = spark.read.format("jdbc")
    .option("url", "jdbc:mysql://localhost:3306/dbRel")
    .option("driver", "com.mysql.jdbc.Driver")
    .option("user", "root")
    .option("password", "1")
    .option("dbtable", "User")
    .load()
  //写入另外一张表
  df.write.mode(SaveMode.Append).format("jdbc")
    .option("url", "jdbc:mysql://localhost:3306/dbRel")
    .option("driver", "com.mysql.jdbc.Driver")
    .option("user", "root")
    .option("password", "1")
    .option("dbtable", "user1")
    .save()
  spark.stop()
}
```