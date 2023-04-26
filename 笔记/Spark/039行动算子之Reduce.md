### 介绍

聚集 RDD 中的所有元素，先聚合分区内数据，再聚合分区间数据

### 例子

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)

  val rdd: RDD[Int] = context.makeRDD(List(1, 2, 3, 4))
  val sum = rdd.reduce(_ + _)
  println(sum)
  context.stop()
}
```

