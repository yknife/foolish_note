### 介绍

将数据根据指定的规则进行分组, 分区默认不变，但是数据会被打乱重新组合，我们将这样 的操作称之为 shuffle。极限情况下，数据可能被分在同一个分区中 一个组的数据在一个分区中，但是并不是说一个分区中只有一个组

### 例子

按首字母分组

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.makeRDD(List("Hello","Hadoop","Spark","Scala"),2)
  rdd.saveAsTextFile("out1")

  val rddGB = rdd.groupBy(_.charAt(0))
  rddGB.saveAsTextFile("out2")
  rddGB.collect().foreach(println)
  context.stop()
}
```

从服务器日志数据 apache.log 中获取每个时间段访问量。（按整点分组）

```scala
def main(args: Array[String]): Unit = {
  val conf: SparkConf = new SparkConf()
    .setMaster("local[*]")
    .setAppName("Spark")
  val context = new SparkContext(conf)
  val rdd = context.textFile("datas/apache.log")
  val rddGB = rdd.groupBy(
    line => {
      val datas = line.split(" ")
      val time = datas(3).split(":")
      time(1)
    }
  )

  rddGB.map{ //偏函数
    case (hour,it) => {
      (hour,it.size)
    }
  }.collect().foreach(println)
  context.stop()
}
```

结果

```log
(06,366)
(20,486)
(19,493)
(15,496)
(00,361)
(02,365)
(04,355)
(22,346)
(17,484)
(13,475)
(11,459)
(08,345)
(14,498)
(21,453)
(09,364)
(18,478)
(16,473)
(03,354)
(07,357)
(12,462)
(05,371)
(01,360)
(10,443)
(23,356)
```

