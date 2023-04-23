### 介绍

类似于 SQL 语句的左外连接

### 例子

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(("a",1),("b",2),("c",3)))
  val rdd1 = context.makeRDD(List(("a",4),("b",5)))
  val rddNew = rdd.leftOuterJoin(rdd1)
  rddNew.collect().foreach(println)
  context.stop()
}
//结果
(a,(1,Some(4)))
(b,(2,Some(5)))
(c,(3,None))
```

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(("a",1),("b",2),("c",3)))
  val rdd1 = context.makeRDD(List(("a",4),("b",5)))
  val rddNew = rdd.leftOuterJoin(rdd1)
  rddNew.collect().foreach(println)
  context.stop()
}
//结果
(a,(Some(1),4))
(b,(Some(2),5))
```

