### 准备数据

```scala
def main(args: Array[String]): Unit = {
  System.setProperty("HADOOP_USER_NAME", "root")
  val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlHive")
  val spark = SparkSession.builder().enableHiveSupport().config(sparkConf).getOrCreate()
  spark.sql("use hive")
  spark.sql(
    """
      |CREATE TABLE `user_visit_action`(
      | `date` string,
      | `user_id` bigint,
      | `session_id` string,
      | `page_id` bigint,
      | `action_time` string,
      | `search_keyword` string,
      | `click_category_id` bigint,
      | `click_product_id` bigint,
      | `order_category_ids` string,
      | `order_product_ids` string,
      | `pay_category_ids` string,
      | `pay_product_ids` string,
      | `city_id` bigint)
      |row format delimited fields terminated by '\t'
      |""".stripMargin)
  spark.sql(
    """
      |load data local inpath 'datas/user_visit_action.txt' into table hive.user_visit_action
      """.stripMargin)
  spark.sql(
    """
      |CREATE TABLE `product_info`(
      | `product_id` bigint,
      | `product_name` string,
      | `extend_info` string)
      |row format delimited fields terminated by '\t'
      |""".stripMargin)
  spark.sql(
    """
      |load data local inpath 'datas/product_info.txt' into table hive.product_info
      |""".stripMargin)
  spark.sql(
    """
      |CREATE TABLE `city_info`(
      | `city_id` bigint,
      | `city_name` string,
      | `area` string)
      |row format delimited fields terminated by '\t'
      |""".stripMargin)
  spark.sql(
    """
      |load data local inpath 'datas/city_info.txt' into table hive.city_info
      |""".stripMargin)
  spark.stop()
}
```

### 需求部分实现

```scala
def main(args: Array[String]): Unit = {
  System.setProperty("HADOOP_USER_NAME", "root")
  val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlExample")
  val spark = SparkSession.builder().enableHiveSupport().config(sparkConf).getOrCreate()
  spark.sql("use hive")
  spark.sql(
    """
      |select
      |  *
      |from (
      |  select
      |   *,
      |   rank() over (partition by area order by click_count desc) as rank
      |  from (
      |   select
      |    t3.area,
      |    t3.product_name,
      |    count(*) as click_count
      |   from (
      |    select
      |     u.*,
      |     c.area,
      |     p.product_name
      |    from user_visit_action u
      |    join product_info p on u.click_product_id = p.product_id
      |    join city_info c on u.city_id = c.city_id
      |    where u.click_product_id > -1
      |   )t3
      |   group by t3.area,t3.product_name
      |  )t1
      |)t2 where t2.rank <= 3
      |""".stripMargin).show()
  spark.stop()
}
```

### 需求完整实现

```scala
def main(args: Array[String]): Unit = {
  System.setProperty("HADOOP_USER_NAME", "root")
  val sparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSqlExample1")
  val spark = SparkSession.builder().enableHiveSupport().config(sparkConf).getOrCreate()
  spark.sql("use hive")
  spark.sql(
    """
      |select
      |  u.*,
      |  c.area,
      | c.city_name,
      |  p.product_name
      |from user_visit_action u
      |join product_info p on u.click_product_id = p.product_id
      |join city_info c on u.city_id = c.city_id
      |where u.click_product_id > -1
      |""".stripMargin).createOrReplaceTempView("t1")

  spark.udf.register("remark_city",functions.udaf(new CityRemarkUDAF))
  spark.sql(
    """
      |select
      |  t1.area,
      |  t1.product_name,
      |  count(*) as click_count,
      | remark_city(t1.city_name) as city_remark
      |from t1
      |group by t1.area,t1.product_name
      |""".stripMargin).createOrReplaceTempView("t2")

  spark.sql(
    """
      |select
      |  *,
      |  rank() over (partition by area order by click_count desc) as rank
      |from t2
      |""".stripMargin).createOrReplaceTempView("t3")

  spark.sql(
    """
      |select
      |  *
      |from t3 where t3.rank <= 3
      |""".stripMargin).show(false)


  spark.stop()
}

case class Buffer(var total:Long,var cityMap:mutable.Map[String,Long]);

class CityRemarkUDAF extends Aggregator[String,Buffer,String]{
  override def zero: Buffer = {
    Buffer(0L,mutable.Map())
  }

  override def reduce(buff: Buffer, city: String): Buffer = {
    buff.total += 1
    val cityCount = buff.cityMap.getOrElse(city, 0L)
    buff.cityMap.update(city,cityCount+1)
    buff
  }

  override def merge(buff1: Buffer, buff2: Buffer): Buffer = {
    buff1.total = buff1.total+buff2.total
    val map1: mutable.Map[String, Long] = buff1.cityMap
    val map2: mutable.Map[String, Long] = buff2.cityMap
    val cm = map1.foldLeft(map2) {
      case (m, (k, v)) => {
        val cityCount = m.getOrElse(k, 0L)
        m.update(k, cityCount + v)
        m
      }
    }
    buff1.cityMap = cm
    buff1
  }

  override def finish(reduction: Buffer): String = {
    val total = reduction.total
    val remarkList = ListBuffer[String]()
    val cityList: List[(String, Long)] = reduction.cityMap.toList.sortWith {
      case (left, right) => {
        left._2 > right._2
      }
    }
    val hasMore = cityList.size > 2
    val subList = cityList.take(2)
    var psum = 0L
    subList.foreach {
      case (city, cnt) => {
        val percent = (cnt * 100) / total
        remarkList.append(s"$city$percent%")
        psum += percent
      }
    }
    if(hasMore){
      val remain = 100 - psum
      remarkList.append(s"其他$remain%")
    }
    remarkList.mkString(",")
  }

  override def bufferEncoder: Encoder[Buffer] = Encoders.product

  override def outputEncoder: Encoder[String] = Encoders.STRING
}

+----+------------+-----------+-------------------------+----+
|area|product_name|click_count|city_remark              |rank|
+----+------------+-----------+-------------------------+----+
|华东|商品_86     |371        |上海16%,杭州15%,其他69%  |1   |
|华东|商品_47     |366        |杭州15%,青岛15%,其他70%  |2   |
|华东|商品_75     |366        |上海17%,无锡15%,其他68%  |2   |
|西北|商品_15     |116        |西安54%,银川45%          |1   |
|西北|商品_2      |114        |银川53%,西安46%          |2   |
|西北|商品_22     |113        |西安54%,银川45%          |3   |
|华南|商品_23     |224        |厦门29%,福州24%,其他47%  |1   |
|华南|商品_65     |222        |深圳27%,厦门26%,其他47%  |2   |
|华南|商品_50     |212        |福州27%,深圳25%,其他48%  |3   |
|华北|商品_42     |264        |郑州25%,保定25%,其他50%  |1   |
|华北|商品_99     |264        |北京24%,郑州23%,其他53%  |1   |
|华北|商品_19     |260        |郑州23%,保定20%,其他57%  |3   |
|东北|商品_41     |169        |哈尔滨35%,大连34%,其他31%|1   |
|东北|商品_91     |165        |哈尔滨35%,大连32%,其他33%|2   |
|东北|商品_58     |159        |沈阳37%,大连32%,其他31%  |3   |
|东北|商品_93     |159        |哈尔滨38%,大连37%,其他25%|3   |
|华中|商品_62     |117        |武汉51%,长沙48%          |1   |
|华中|商品_4      |113        |长沙53%,武汉46%          |2   |
|华中|商品_57     |111        |武汉54%,长沙45%          |3   |
|华中|商品_29     |111        |武汉50%,长沙49%          |3   |
+----+------------+-----------+-------------------------+----+
```