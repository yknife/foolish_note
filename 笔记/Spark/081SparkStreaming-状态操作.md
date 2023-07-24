## 有状态操作

### 概念

​	UpdateStateByKey 原语用于记录历史记录，有时，我们需要在 DStream 中跨批次维护状态(例 如流计算中累加 wordcount)。针对这种情况，updateStateByKey()为我们提供了对一个状态变量 的访问，用于键值对形式的 DStream。给定一个由(键，事件)对构成的 DStream，并传递一个指 定如何根据新的事件更新每个键对应状态的函数，它可以构建出一个新的 DStream，其内部数 据为(键，状态) 对。

updateStateByKey() 的结果会是一个新的 DStream，其内部的 RDD 序列是由每个时间区间对 应的(键，状态)对组成的。

updateStateByKey 操作使得我们可以在用新信息进行更新时保持任意的状态。为使用这个功 能，需要做下面两步：

1. 定义状态，状态可以是一个任意的数据类型。
2. 定义状态更新函数，用此函数阐明如何使用之前的状态和来自输入流的新值对状态进行更 新。

使用 updateStateByKey 需要对检查点目录进行配置，会使用检查点来保存状态。

### 代码

```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new
      SparkConf().setMaster("local[*]").setAppName("UpdateStateStreamWordCount")
  val ssc = new StreamingContext(sparkConf, Seconds(3))
  //设置检查点
  ssc.checkpoint("./cp")
  val lineStream: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 9999)
  val wordStream: DStream[String] = lineStream.flatMap(_.split(" "))
  val tupleStream: DStream[(String, Int)] = wordStream.map((_, 1))
  val updateStateStream: DStream[(String, Int)] = tupleStream.updateStateByKey(
    (seq: Seq[Int], opt: Option[Int]) => {
      val curCount = seq.sum
      val previousCount = opt.getOrElse(0)
      Option(previousCount + curCount)
    }
  )
  updateStateStream.print()
  ssc.start()
  ssc.awaitTermination()
}
```

### NetCat

```sh
nc -lk 9999
```

### 总结

​	从输出结果的层面上看，对比reduceByKey版本SparkStreaming的wordCount程序，reduceByKey只统计当前周期的数据，而updateStateByKey可以跨周期进行统计。
