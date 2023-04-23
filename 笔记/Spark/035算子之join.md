### 介绍

在类型为(K,V)和(K,W)的 RDD 上调用，返回一个相同 key 对应的所有元素连接在一起的 (K,(V,W))的 RDD

### 例子

```scala
val conf: SparkConf = new SparkConf()
      .setMaster("local[*]")
      .setAppName("Spark")
    val context = new SparkContext(conf)
    val rdd = context.makeRDD(List(("a",1),("b",2),("c",3)))
    val rdd1 = context.makeRDD(List(("a",4),("b",5))) // 结果(a,(1,4))，(b,(2,5)),没有c，与sql中inner join的特性很类似
//    val rdd1 = context.makeRDD(List(("c",4),("b",5),("a",6))) //区别拉链，join时元素在List中的位置不用一一对应
    //    val rdd1 = context.makeRDD(List(("a",4),("b",5),("c",6))) //正常场景
    val rddNew = rdd.join(rdd1)
    rddNew.collect().foreach(println)
    context.stop()
```

