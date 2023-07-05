```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlUDF")
  val spark = SparkSession.builder().config(sparkConf).getOrCreate()
  //读取 json 文件 创建 DataFrame
  val df: DataFrame = spark.read.json("datas/user.json")
  df.createOrReplaceTempView("user")
  //注册UDF函数
  spark.udf.register("prefixName",(n:String)=>"username:"+n)
  //使用UDF函数
  spark.sql("select prefixName(username),age from user").show()
  spark.stop()
}
```