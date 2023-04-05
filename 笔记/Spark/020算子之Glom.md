### 介绍

将同一个分区的数据直接转换为相同类型的内存数组进行处理，分区不变

### 例子

计算所有分区最大值求和（分区内取最大值，分区间最大值求和）

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(1,2,3,4),2)

  val rddGlom: RDD[Array[Int]] = rdd.glom()

  val rddMap = rddGlom.map(
    arr => arr.max
  )

  println(rddMap.collect().sum)

  context.stop()
}
```

