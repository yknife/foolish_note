## sql基本使用

1. 对DataFrame创建一个临时表，当前session创建的视图只在当前视图有效，想全局访问可以使用createGlobalTempView

   ```sh
    df.createOrReplaceTempView("people")
   ```

2. 对于 DataFrame 创建一个全局表，

   ```sh
    df.createGlobalTempView("people")
    spark.sql("select * from global_temp.people").show
    spark.newSession().sql("select * from global_temp.people").show
   ```

   