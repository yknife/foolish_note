### 优雅关闭

```scala
object WordCountClose {

  def main(args: Array[String]): Unit = {
    val sparkConf = new
        SparkConf().setMaster("local[*]").setAppName("wordCountClose")
    val ssc = new StreamingContext(sparkConf, Seconds(3))
    val lineStream: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 8888)
    val mapStream: DStream[(String, Int)] = lineStream.map((_, 1))
    val reduceStream: DStream[(String, Int)] = mapStream.reduceByKey(_ + _)
    reduceStream.print()
    ssc.start()
    new Thread(()=>{
      TimeUnit.SECONDS.sleep(10)

      val state: StreamingContextState = ssc.getState()
      //只有当SparkStreaming的上下文状态为Active才关闭
      if(state==StreamingContextState.ACTIVE){
        //第二个参数true表示优雅的关闭，会等已经接收的数据处理完再关闭
        ssc.stop(true,true)
      }
      System.exit(0)

    }).start()
    ssc.awaitTermination()
  }

}
```

### 恢复

```scala
object WordCountClose_Resume {

  def createSSC(): _root_.org.apache.spark.streaming.StreamingContext = {
    val update: (Seq[Int], Option[Int]) => Some[Int] = (values: Seq[Int], status: Option[Int]) => {
      //当前批次内容的计算
      val sum: Int = values.sum
      //取出状态信息中上一次状态
      val lastStatu: Int = status.getOrElse(0)
      Some(sum + lastStatu)
    }

    val sparkConf: SparkConf = new
        SparkConf().setMaster("local[*]").setAppName("SparkTest")
    val ssc = new StreamingContext(sparkConf, Seconds(5))
    ssc.checkpoint("./ck")
    val line: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 8888)
    val wordAndOne: DStream[(String, Int)] = line.map((_, 1))
    val wordAndCount: DStream[(String, Int)] = wordAndOne.updateStateByKey(update)
    wordAndCount.print()
    ssc
  }
  def main(args: Array[String]): Unit = {
    val ssc: StreamingContext = StreamingContext.getActiveOrCreate("./ck", () =>
      createSSC())
    ssc.start()
    new Thread(()=>{
      TimeUnit.SECONDS.sleep(20)
      val state: StreamingContextState = ssc.getState()
      if(state==StreamingContextState.ACTIVE){
        ssc.stop(true,true)
      }
      System.exit(0)
    }).start()
    ssc.awaitTermination()
  }
}
```
