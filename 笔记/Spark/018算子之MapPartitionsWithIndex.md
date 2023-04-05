例子：查看数据所在的分区

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(1, 2, 3, 4),2)
  val rddMPI = rdd.mapPartitionsWithIndex(
    (idx, it) => {
      //
      it.map(num => {
        (idx, num)
      })
    }
  )
  rddMPI.collect().foreach(println)
  context.stop()
}
```