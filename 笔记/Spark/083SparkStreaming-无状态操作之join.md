```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new
      SparkConf().setMaster("local[*]").setAppName("JoinWordCount")
  val ssc = new StreamingContext(sparkConf, Seconds(5))

  val lineStream9: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 9999)
  val lineStream8: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 8888)

  val ds8: DStream[(String, Int)] = lineStream8.map((_, 8))
  val ds9: DStream[(String, Int)] = lineStream9.map((_, 9))

  val joinDS: DStream[(String, (Int, Int))] = ds8.join(ds9)

  joinDS.print()

  ssc.start()
  ssc.awaitTermination()
}
```

```log
Time: 1690105205000 ms
-------------------------------------------
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
(a,(8,9))
...
```

兄弟们，值虽然是一样的，但是这个是有状态的，性能更快，会保存上一次的窗口值
