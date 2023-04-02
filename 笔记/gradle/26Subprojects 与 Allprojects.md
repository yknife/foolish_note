### Subprojects 与 Allprojects

allprojects 是对所有project**(包括Root Project+ child Project[当前工程和所有子工程])**的进行统一配置，而subprojects

是对**所有Child Project 的进行统一配置**。测试如下：

allprojects {

tasks.create('hello') { doLast {

task ->

println "project name is $task.project.name"

}

}

}

subprojects {

hello.doLast{ task->

println "here is subprojects $task.project.name"

}

}

通常在 subprojects 和allprojects 中:









**allprojects****(**){ //本质Project中的allprojects方法，传递一个闭包作为参数。

apply **plugin**: **'java'**

ext {

junitVersion = **'4.10'**

..

}

task allTask{

...

}

repositories {

...

}

dependencies {

...

}

}

**subprojects**()**{**

**…//****同上面allprojects中的方法****。**

**}**

**拓展 1:** 如果是直接**在根project 配置** repositories 和 dependencies 则**只针对根工程有效。拓展:**我们也可以在对单个 Project 进行单独配置：

project(**'subject01'**) **{**

task subject01 **{**

doLast **{**

println **'for subject01'**

**}**

**}**

**}**

执行gradle build 指令即可查看测试效果。