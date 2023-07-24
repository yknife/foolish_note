### 对比Map与Transform

```scala
def main(args: Array[String]): Unit = {
  val sparkConf = new
      SparkConf().setMaster("local[*]").setAppName("TransformWordCount")
  val ssc = new StreamingContext(sparkConf, Seconds(3))
  val lineStream: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 9999)
	//从功能上讲map和transform都是转换的操作，如果考虑简单，直接使用map就可以了，那transform到底有什么作用呢？
  lineStream.map(str=>str)
  lineStream.transform(rdd=>{rdd.map(str=>str)})
  ssc.start()
  ssc.awaitTermination()
}
```

```scala
//Driver
lineStream.map(
  str=>{
    //Executor
    str
  }
)
//使用场景
//1.DStream功能不完善
//2.需要代码周期性执行
//Driver
lineStream.transform(
  rdd=>{
    //Driver,除了算子以外的操作都在driver端执行,(周期性执行)
    rdd.map(
      str=>{
        //Executor
        str
      }
    )
  }
)
```

