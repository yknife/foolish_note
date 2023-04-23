### 介绍

在类型为(K,V)和(K,W)的 RDD 上调用，返回一个(K,(Iterable,Iterable))类型的 RDD

### 例子

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List(("a",1),("b",2),("c",3),("c",4)))
  val rdd1 = context.makeRDD(List(("a",4),("b",5),("c",6)))
  val rddNew: RDD[(String, (Iterable[Int], Iterable[Int]))] = rdd.cogroup(rdd1)
  rddNew.collect().foreach(println)
  context.stop()
}
```

像是各rdd先connect，然后两个rdd再根据key分组的感觉

```log
(a,(CompactBuffer(1),CompactBuffer(4)))
(b,(CompactBuffer(2),CompactBuffer(5)))
(c,(CompactBuffer(3, 4),CompactBuffer(6)))
```

