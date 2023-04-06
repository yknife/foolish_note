### 函数签名

* def distinct()(implicit ord: Ordering[T] = null): RDD[T] 

* def distinct(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T]

### 例子

```scala
val dataRDD = sparkContext.makeRDD(List(
 1,2,3,4,1,2
),1)
val dataRDD1 = dataRDD.distinct()
val dataRDD2 = dataRDD.distinct(2)
```

### 源码剖析

![image-20230407002038761](024算子之distinct.assets/image-20230407002038761.png)

![image-20230407002438694](024算子之distinct.assets/image-20230407002438694.png)