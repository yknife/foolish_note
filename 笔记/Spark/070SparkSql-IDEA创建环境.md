## IDEA创建环境

### 添加依赖

```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-sql_2.12</artifactId>
    <version>3.0.0</version>
</dependency>
```

### 代码

```scala
object SparkSqlBasic {

  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSql")
    val spark = SparkSession.builder().config(sparkConf).getOrCreate()
    //RDD=>DataFrame=>DataSet 转换需要引入隐式转换规则，否则无法转换
    //spark 不是包名，是上下文环境对象名
    import spark.implicits._
    //读取 json 文件 创建 DataFrame
    val df: DataFrame = spark.read.json("datas/user.json")
    df.show()
    //SQL 风格语法
    df.createOrReplaceTempView("user")
    spark.sql("select avg(age) from user").show()
    //DSL 风格语法
    df.select("username","age").show()
    df.filter($"age">30).show()
    df.select('age+1).show()
    //*****RDD=>DataFrame=>DataSet*****
    val rdd = spark.sparkContext.makeRDD(Seq((1, "yknife", 30), (2, "leo", 31)))
    val df1 = rdd.toDF("id", "name", "age")
    df1.show()
    val ds: Dataset[Emp] = df1.as[Emp]
    ds.show()
    //*****DataSet=>DataFrame=>RDD*****
    val df2 = ds.toDF()
    //RDD 返回的 RDD 类型为 Row，里面提供的 getXXX 方法可以获取字段值，类似 jdbc 处理结果集，
    //但是索引从 0 开始
    val rdd1: RDD[Row] = df2.rdd
    rdd1.collect().foreach(l=>println(l.get(1)))
    //*****RDD=>DataSet*****
    val ds1: Dataset[Emp] = rdd.map {
      case (id, name, age) => Emp(id, name, age)
    }.toDS()
    ds1.show()
    //*****DataSet=>=>RDD*****
    val rdd2: RDD[Emp] = ds1.rdd
    rdd2.collect().foreach(println)

    spark.stop()
  }
  case class Emp(id:Int,name:String,age:Int)
}
```
