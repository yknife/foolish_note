```scala
// 保存成 Text 文件
rdd.saveAsTextFile("output")
// 序列化成对象保存到文件
rdd.saveAsObjectFile("output1")
// 保存成 Sequencefile 文件,需要K-V类型rdd才能使用这个方法
rdd.map((_,1)).saveAsSequenceFile("output2")
```

