例子：将混合list和int的数据统统打散

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(List(1,2),3,List(3,4)))
  val rddFM = rdd.flatMap(
    data => {
      data match {
        case list: List[_] => list
        case _ => List(data)
      }
    }
  )

  rddFM.collect().foreach(println)

  context.stop()
}
```