### 介绍

当分区内计算规则和分区间计算规则相同时，aggregateByKey 就可以简化为 foldByKey

### 例子

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd1 = context.makeRDD(List(("a",1),("a",2),("b",3),("a",4)),2)
  val rddNew = rdd1.foldByKey(0)(_+_)
  rddNew.collect().foreach(println)
  context.stop()
}
```

