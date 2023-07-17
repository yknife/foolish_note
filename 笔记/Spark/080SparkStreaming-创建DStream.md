### 创建 QueueInputDStream

```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new
      SparkConf().setMaster("local[*]").setAppName("QueueStreamExample")
  val ssc = new StreamingContext(sparkConf, Seconds(3))
  val queue = new mutable.Queue[RDD[Int]]()
  //queueStream的第二个参数oneAtATime，是否一次只消费1个rdd
  //Whether only one RDD should be consumed from the queue in every interval
  val queueStream: InputDStream[Int] = ssc.queueStream(queue, false)
  val mapStream: DStream[(Int, Int)] = queueStream.map((_, 1))
  val reduceStream: DStream[(Int, Int)] = mapStream.reduceByKey(_ + _)
  reduceStream.print()
  ssc.start()
  for(i<-1 to 5){
    queue += ssc.sparkContext.makeRDD(1 to 300, 10)
    Thread.sleep(2000)
  }
  ssc.awaitTermination()
}
```

### 自定义数据源

```scala
object MyReceiverStreamExample {

  def main(args: Array[String]): Unit = {
    val sparkConf = new
        SparkConf().setMaster("local[*]").setAppName("MyReceiverStreamExample")
    val ssc = new StreamingContext(sparkConf, Seconds(3))
    //创建自定义采集器
    val receiverStream: ReceiverInputDStream[String] = ssc.receiverStream(new MyReceiver)
    receiverStream.print()
    ssc.start()
    ssc.awaitTermination()
  }

  class MyReceiver extends Receiver[String](StorageLevel.MEMORY_ONLY){
    var flag = true;
    override def onStart(): Unit = {
      new Thread(()=>{
        while (flag){
          val message = "采集的数据为：" + new Random().nextInt(100)
          //将采集到的数据封装成DStream
          store(message)
          Thread.sleep(100)
        }
      }).start()
    }

    override def onStop(): Unit = {
      flag = false
    }
  }

}
```

### Kafka数据源

```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new
      SparkConf().setMaster("local[*]").setAppName("KafkaSource")
  val ssc = new StreamingContext(sparkConf, Seconds(3))
  val kafkaPara: Map[String, Object] = Map[String, Object](
    //设置broker-list
    ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG ->
      "localhost:9092",
    //设置消费组
    ConsumerConfig.GROUP_ID_CONFIG -> "test_group",
    "key.deserializer" ->
      "org.apache.kafka.common.serialization.StringDeserializer",
    "value.deserializer" ->
      "org.apache.kafka.common.serialization.StringDeserializer"
  )

  val kafkaDStream: InputDStream[ConsumerRecord[String, String]] = KafkaUtils.createDirectStream[String, String](
    ssc,
    //本地策略，采集节点与计算节点如何做匹配
    //PreferConsistent:Use this in most cases, it will consistently distribute partitions across all executors.
    LocationStrategies.PreferConsistent,
    //消费者策略
    ConsumerStrategies.Subscribe[String, String](Set("test_topic"), kafkaPara)
  )
  kafkaDStream.map(_.value()).print()
  ssc.start()
  ssc.awaitTermination()
}
```
