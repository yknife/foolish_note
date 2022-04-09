## 一个语法一个Demo系列：Shell function 函数返回值

------

shell 函数返回值的三种方式：

1. 使用 return 返回
2. 使用 echo 返回
3. 使用全局变量实现函数返回值

------

### 1，使用 return 返回

使用 `$?` 来获取返回值 这种方式局限性：只能返回整形数值，不能返回字符串，一般用于返回执行结果，0表示成功，其余表示执行失败的错误号

**$?** 仅对其上一条指令负责，一旦函数返回后其返回值没有立即保存入参数，那么其返回值将不再能通过 **$?** 获得。

shell_function_return.sh

```
#!/bin/bash
# Author: laigeDemo.com

function1() {
	return 1
}

echo "call function1"
function1
ret=$?
echo "function1 return:$ret"
```



**运行结果：**

```
call function1
function1 return:1
```

### 2，使用 echo 返回

使用[ 命令替换$() ](https://laigedemo.com/linux_shell/shell_cmd_substitute.html)来接收返回值 这种方式局限性：函数体中不能加其它 echo 信息



shell_function_return2.sh

```
#!/bin/bash
# Author: laigeDemo.com

function2() {
	echo "laideDemo"
}

echo "call function2"
ret=$(function2)
echo "function2 return:$ret"
```



**运行结果：**

```
call function2
function2 return:laideDemo
```

### 3，使用全局变量实现函数返回值

函数体中设置全局变量，调用函数后读取全局变量的值。 特点：书写繁琐，但结构简单容易理解



shell_function_return3.sh

```
#!/bin/bash
# Author: laigeDemo.com

g_ret=""
function3() {
	g_ret="hi laigeDemo"
}

echo "call function3"
function3
echo "function3 return:$g_ret"
```



**运行结果：**

```
call function3
function3 return:hi laigeDemo
```