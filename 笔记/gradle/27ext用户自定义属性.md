### ext 用户自定义属性

Project 和 Task 都允许用户添加额外的自定义属性，要添加额外的属性，通过应用所属对象的ext 属性即可实现。添加之后可以通过 ext 属性对自定义属性读取和设置，如果要同时添加多个自定义属性,可以通过 ext 代码块:

*//*自定义一个Project的属性

ext.age = **18**

//通过代码块同时自定义多个属性ext **{**

phone = **19292883833**

address=**"北京尚硅谷"**

**}**

task extCustomProperty **{**

*//*在task中自定义属性

ext **{**

desc = **"奥利给"**

**}**

doLast **{**

println **" 年 龄 是 ：**$**{**age**}****"** println **"电话是：**$**{**phone**}****"** println **"地址是：**$**{**address**}****"** println **"尚硅谷：**$**{**desc**}****"**

**}**

**}**

测试：通过 gradle extCustomProperty

输出结果为：









年龄是：18

电话是：19292883833

地址是：北京尚硅谷尚硅谷：奥利给

**拓展 1**: ext 配置的是用户自定义属性，而gradle.properties 中一般定义 系统属性、环境变量、项目属性、JVM 相关配置信息。例如

gradle.properties 文件案例：加快构建速度的，gradle.properties 文件中的属性会自动在项目运行时加载。

\## 设置此参数主要是编译下载包会占用大量的内存，可能会内存溢出

org.gradle.jvmargs=-Xms4096m -Xmx8192m ## 开启gradle缓存

org.gradle.caching=true #开启并行编译org.gradle.parallel=true #启用新的孵化模式

org.gradle.configureondemand=true #开启守护进程org.gradle.daemon=true

详细请参考：[https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties](https://docs.gradle.org/current/userguide/build_environment.html#sec%3Agradle_configuration_properties)