```scala
object WordCount {


  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf()
      .setMaster("local[*]")
      .setAppName("Spark")
    val sc = new SparkContext(conf)
    wordCount11(sc)
    sc.stop()
  }
	//GroupBy
  def wordCount1(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val rddGroup: RDD[(String, Iterable[String])] = rddFlatMap.groupBy(word => word)
    val rddMapValues: RDD[(String, Int)] = rddGroup.mapValues(it => it.size)
    rddMapValues.collect().foreach(println)
  }
	//GroupByKey
  def wordCount2(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val wordOne = rddFlatMap.map((_, 1))
    val rddGroupByKey: RDD[(String, Iterable[Int])] = wordOne.groupByKey()
    val rddMapValues: RDD[(String, Int)] = rddGroupByKey.mapValues(it => it.size)
    rddMapValues.collect().foreach(println)
  }
	//GroupByKey有shuffle操作性能不好，使用ReduceByKey优化
  def wordCount3(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val wordOne = rddFlatMap.map((_, 1))
    val rddReduceByKey: RDD[(String, Int)] = wordOne.reduceByKey(_ + _)
    rddReduceByKey.collect().foreach(println)
  }

  //AggregateByKey
  def wordCount4(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val wordOne = rddFlatMap.map((_, 1))
    val rddAggByKey: RDD[(String, Int)] = wordOne.aggregateByKey(0)(_ + _,_+_)
    rddAggByKey.collect().foreach(println)
  }
	//FoldByKey,比AggregateByKey少一个参数
  def wordCount5(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val wordOne = rddFlatMap.map((_, 1))
    val rddAggByKey: RDD[(String, Int)] = wordOne.foldByKey(0)(_ + _)
    rddAggByKey.collect().foreach(println)
  }

  //CombineByKey,与ReduceByKey、AggregateByKey、FoldByKey都调用相同的方法combineByKeyWithClassTag
  def wordCount6(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val wordOne = rddFlatMap.map((_, 1))
    val rddCombineByKey: RDD[(String, Int)] = wordOne.combineByKey(
      v => v,
      (x: Int, y) => x + y,
      (x: Int, y: Int) => x + y
    )
    rddCombineByKey.collect().foreach(println)
  }

  //CountByKey
  def wordCount7(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val wordOne = rddFlatMap.map((_, 1))
    val result: collection.Map[String, Long] = wordOne.countByKey()
    println(result)
  }

  //CountByValue
  def wordCount8(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val result: collection.Map[String, Long] = rddFlatMap.countByValue()
    println(result)
  }

  //Reduce
  def wordCount9(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val rddMap: RDD[mutable.Map[String, Long]] = rddFlatMap.map(
      word => mutable.Map[String, Long]((word, 1))
    )
    val result: mutable.Map[String, Long] = rddMap.reduce(
      (map1, map2) => {
        map2.foreach {
          case (k, v) => {
            val oldValue: Long = map1.getOrElse(k, 0L)
            map1.update(k, oldValue + v)
          }
        }
        map1
      }
    )
    println(result)
  }

  //Aggregate
  def wordCount10(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val rddMap: RDD[mutable.Map[String, Long]] = rddFlatMap.map(
      word => mutable.Map[String, Long]((word, 1))
    )
    val result: mutable.Map[String, Long] = rddMap.aggregate(mutable.Map[String, Long]())(
      (map1, map2) => {
        map2.foreach {
          case (k, v) => {
            val oldValue: Long = map1.getOrElse(k, 0L)
            map1.update(k, oldValue + v)
          }
        }
        map1
      },
      (map1, map2) => {
        map2.foreach {
          case (k, v) => {
            val oldValue: Long = map1.getOrElse(k, 0L)
            map1.update(k, oldValue + v)
          }
        }
        map1
      }
    )
    println(result)
  }

  //Fold
  def wordCount11(sc:SparkContext): Unit ={
    val rdd: RDD[String] = sc.makeRDD(List("Hello Spark", "Hello Scala"))
    val rddFlatMap = rdd.flatMap(_.split(" "))
    val rddMap: RDD[mutable.Map[String, Long]] = rddFlatMap.map(
      word => mutable.Map[String, Long]((word, 1))
    )
    val result: mutable.Map[String, Long] = rddMap.fold(mutable.Map[String, Long]())(
      (map1, map2) => {
        map2.foreach {
          case (k, v) => {
            val oldValue: Long = map1.getOrElse(k, 0L)
            map1.update(k, oldValue + v)
          }
        }
        map1
      }
    )
    println(result)
  }

}
```