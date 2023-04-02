### 任务的 onlyIf 断言

断言就是一个条件表达式。Task 有一个 onlyIf 方法。它接受一个闭包作为参数，如果该闭包返回 true 则该任务执行， 否则跳过。这有很多用途，比如控制程序哪些情况下打什么包，什么时候执行单元测试，什么情况下执行单元测试的时候不执行网络测试等。具体案例如下所示：



task hello **{**

doLast **{**

println **'hello 尚硅谷的粉丝们'**

**}**

**}**



hello.onlyIf **{** !project.hasProperty(**'fensi'**) **}**

测试：通过-P 为Project 添加fensi 属性

gradle hello -Pfensi