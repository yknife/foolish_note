### Spark3.0以后版本，可以在sql中直接使用UDAF函数

```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlUDAF1")
  val spark = SparkSession.builder().config(sparkConf).getOrCreate()
  //读取 json 文件 创建 DataFrame
  val df: DataFrame = spark.read.json("datas/user.json")
  df.createOrReplaceTempView("user")
  //3.0之前没有functions.udaf()函数，不能把强类型函数转为弱类型，不能完成注册
  spark.udf.register("myAvg",functions.udaf(new MyAvgUDAF()))
  spark.sql("select myAvg(age) from user").show()
  spark.stop()
}
case class Buff(var total:Long,var count:Long)
class MyAvgUDAF extends Aggregator[Long,Buff,Long]{
  override def zero: Buff = {
    Buff(0L,0L)
  }

  override def reduce(b: Buff, a: Long): Buff = {
    b.total = b.total + a
    b.count = b.count + 1
    b
  }

  override def merge(b1: Buff, b2: Buff): Buff = {
    b1.total = b1.total + b2.total
    b1.count = b1.count + b2.count
    b1
  }

  override def finish(reduction: Buff): Long = {
    reduction.total/reduction.count
  }
  //DataSet 默认额编解码器，用于序列化，固定写法
  //自定义类型就是 product 自带类型根据类型选择
  override def bufferEncoder: Encoder[Buff] = Encoders.product

  override def outputEncoder: Encoder[Long] = Encoders.scalaLong
}
```

### 低版本中使用DSL方式实现聚合

```scala
object SparkSqlUDAF2 {

  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlUDAF2")
    val spark = SparkSession.builder().config(sparkConf).getOrCreate()
    import spark.implicits._
    //读取 json 文件 创建 DataFrame
    val df: DataFrame = spark.read.json("datas/user.json")
    //需要隐式转换
    val ds: Dataset[Emp] = df.as[Emp]
    val avgColumn: TypedColumn[Emp, Long] = new MyAvgUDAF().toColumn
    ds.select(avgColumn).show()
    spark.stop()
  }
	//需要样例类
  //注意：这类类型需要是Long不能是Int,否则会报错
  case class Emp(username:String,age:Long)
  case class Buff(var total:Long,var count:Long)
  //输入类型为Emp，代表输入一行数据
  class MyAvgUDAF extends Aggregator[Emp,Buff,Long]{
    override def zero: Buff = {
      Buff(0L,0L)
    }
		//输入类型改为Emp
    override def reduce(b: Buff, a: Emp): Buff = {
      b.total = b.total + a.age
      b.count = b.count + 1
      b
    }

    override def merge(b1: Buff, b2: Buff): Buff = {
      b1.total = b1.total + b2.total
      b1.count = b1.count + b2.count
      b1
    }

    override def finish(reduction: Buff): Long = {
      reduction.total/reduction.count
    }

    override def bufferEncoder: Encoder[Buff] = Encoders.product

    override def outputEncoder: Encoder[Long] = Encoders.scalaLong
  }
}
```