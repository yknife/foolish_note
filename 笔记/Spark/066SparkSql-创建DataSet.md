### 使用样例类创建DataSet

```sh
case class Emp(age:Long,username:String)
val ds = List(Emp(15,"zhangsan"),Emp(16,"lisi")).toDS
ds.show
+---+--------+
|age|username|
+---+--------+
| 15|zhangsan|
| 16|    lisi|
+---+--------+
```

### 使用基本类型List创建

```sh
val ds = List(1,2,3,4).toDS
ds.show
+-----+
|value|
+-----+
|    1|
|    2|
|    3|
|    4|
+-----+
```



