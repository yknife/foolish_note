### 任务入门

可参考官方文档：[https://docs.gradle.org/current/userguide/tutorial_using_tasks.html ](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)

让我们来先看一个例子:

```groovy
task A {
    println "root taskA" 
    doFirst(){
        println "root taskA doFirst"
    }
    doLast(){
        println "root taskA doLast"
    }
}
```



在文件所在的目录执行命令: gradle A。

**提示 1** :task 的配置段是在配置阶段完成

**提示 2** :task 的doFirst、doLast 方法是执行阶段完成，并且doFirst 在doLast 执行之前执行。

**提示 3:**区分任务的配置段和任务的行为,任务的配置段在配置阶段执行,任务的行为在执行阶段执行