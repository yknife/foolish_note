## IDEA Maven 下载源码出现：Cannot download sources Sources not found for: xxx

### 现象

![在这里插入图片描述](Maven无法下载源码.assets/b05a8058671a4194be0667e970a6f65a.png)

### 点击Execute Maven Goal按钮

<img src="Maven无法下载源码.assets/image-20230713215829663.png" alt="image-20230713215829663" style="zoom:50%;" />

```sh
mvn dependency:resolve -Dclassifier=sources
```

执行后，IDEA会重新下载依赖包，下载完后再看源码就没有问题了。
