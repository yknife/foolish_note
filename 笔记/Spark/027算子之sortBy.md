### 介绍

该操作用于排序数据。在排序之前，可以将数据通过 f 函数进行处理，之后按照 f 函数处理 的结果进行排序，默认为升序排列。排序后新产生的 RDD 的分区数与原 RDD 的分区数一 致。中间存在 shuffle 的过程

### 例子

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(4, 2, 3, 6, 1,5),2)
  val rddNew = rdd.sortBy(num => num,true)//默认为true,true为升序，false为降序。结果：[1,2,3],[4,5,6]
  rddNew.saveAsTextFile("out1")
  context.stop()
}
```

### 排序规则可以自定义

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(("1",1),("2",1),("11",1)),2)
  val rddNew = rdd.sortBy(t => t._1,true)//结果[("1",1),("11",1)],[("2",1)]
  rddNew.saveAsTextFile("out1")
  context.stop()
}
```