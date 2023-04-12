### 介绍

可以将数据按照相同的 Key 对 Value 进行两两聚合

### 例子

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd1 = context.makeRDD(List(("a",1),("a",2),("a",3),("b",4)),2)
  val rddNew = rdd1.reduceByKey((x:Int,y:Int)=>{
    println(s"x=${x},y=${y}") //注意：（“b”,4）没有进行聚合，至少需要2个key相同的元组才会聚合
    x+y
  })
  rddNew.collect().foreach(println)
  context.stop()
}
```

```log
x=1,y=2
x=3,y=3
(b,4)
(a,6)
```

