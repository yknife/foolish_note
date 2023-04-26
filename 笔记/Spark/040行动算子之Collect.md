### 介绍

在驱动程序中，以数组 Array 的形式返回数据集的所有元素

### 例子

```scala
val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))
// 收集数据到 Driver
rdd.collect().foreach(println)
```



