```scala
object SparkSqlUDAF {

  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlUDAF")
    val spark = SparkSession.builder().config(sparkConf).getOrCreate()
    //读取 json 文件 创建 DataFrame
    val df: DataFrame = spark.read.json("datas/user.json")
    df.createOrReplaceTempView("user")
    spark.udf.register("myAvg",new MyAvgUDAF())
    spark.sql("select myAvg(age) from user").show()

    spark.stop()
  }

  class MyAvgUDAF extends UserDefinedAggregateFunction{
    //输入数据的结构
    override def inputSchema: StructType = {
      StructType(
        Array(
          StructField("age",LongType)
        )
      )
    }
    //缓冲区的结构
    override def bufferSchema: StructType = {
      StructType(
        Array(
          StructField("total",LongType),
          StructField("count",LongType)
        )
      )
    }
    //输出结果的类型
    override def dataType: DataType = LongType
    //函数的稳定性
    override def deterministic: Boolean = true
    //缓冲区的初始化
    override def initialize(buffer: MutableAggregationBuffer): Unit = {
      //等效与buffer.update(0,0L)
      buffer(0)=0L
      buffer(1)=0L
    }
    //更新缓冲区
    override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
      buffer(0)=buffer.getLong(0)+input.getLong(0)
      buffer(1)=buffer.getLong(1)+1
     }
    //合并缓冲区
    override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
      buffer1(0) = buffer1.getLong(0)+buffer2.getLong(0)
      buffer1(1) = buffer1.getLong(1)+buffer2.getLong(1)
    }
    //计算结果
    override def evaluate(buffer: Row): Any = {
      buffer.getLong(0)/buffer.getLong(1)
    }
  }
}
```