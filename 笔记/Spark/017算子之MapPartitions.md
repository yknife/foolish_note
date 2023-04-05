### 介绍

将待处理的数据以分区为单位发送到计算节点进行处理，这里的处理是指可以进行任意的处 理，哪怕是过滤数据。

### 使用

```scala
def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf()
      .setMaster("local[*]")
      .setAppName("Spark")
    val sc = new SparkContext(conf)
    val rdd = sc.makeRDD(List(1, 2, 3, 4),2)
    val mpRdd = rdd.mapPartitions(it => {
      it.map(_ * 2) //运算时会将一个分区的数据都加载到iterator中
    })
    mpRdd.collect().foreach(println)
    sc.stop()
  }
```

### 思考一个问题：map 和 mapPartitions 的区别？ 

*  数据处理角度 Map 算子是分区内一个数据一个数据的执行，类似于串行操作。而 mapPartitions 算子 是以分区为单位进行批处理操作。 
*  功能的角度 Map 算子主要目的将数据源中的数据进行转换和改变。但是不会减少或增多数据。 MapPartitions 算子需要传递一个迭代器，返回一个迭代器，没有要求的元素的个数保持不变， 所以可以增加或减少数据 
* 性能的角度 Map 算子因为类似于串行操作，所以性能比较低，而是 mapPartitions 算子类似于批处 理，所以性能较高。但是 mapPartitions 算子会长时间占用内存，那么这样会导致内存可能 不够用，出现内存溢出的错误。所以在内存有限的情况下，不推荐使用。使用 map 操作。