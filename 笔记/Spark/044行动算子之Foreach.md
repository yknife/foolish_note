```scala
val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))
// 收集后打印
rdd.map(num=>num).collect().foreach(println)
println("****************")
// 分布式打印
rdd.foreach(println)
```

![image-20230427000646875](044行动算子之Foreach.assets/image-20230427000646875.png)