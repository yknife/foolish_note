

### DataFrame转DataSet

```sh
case class Emp(age:Long,username:String)
val ds = df.as[Emp]
ds.show
+---+--------+
|age|username|
+---+--------+
| 10|  yknife|
| 30|     leo|
+---+--------+
```

### DataSet转DataFrame

```sh
val df = ds.toDF
```



