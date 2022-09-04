1、右键显示JD-[GUI](https://so.csdn.net/so/search?q=GUI&spm=1001.2101.3001.7020)显示包内容，找到“Contents/MacOS/universalJavaApplicationStub.sh”编辑

![img](Mac安装JD-GUI.assets/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yid5a2m6ICFwrA=,size_20,color_FFFFFF,t_70,g_se,x_16.png)

2、找到提示错误的这里，我这里是234-237行

![img](Mac安装JD-GUI.assets/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yid5a2m6ICFwrA=,size_20,color_FFFFFF,t_70,g_se,x_16-20220904183615532.png)

3、注释或删除掉235和237行，并增加JAVACMD="/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/bin/java"

路径中的jdk1.8.0_201.jdk需要改成你自己的安装的版本，之后就可以启动了

![img](Mac安装JD-GUI.assets/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yid5a2m6ICFwrA=,size_20,color_FFFFFF,t_70,g_se,x_16-20220904183619776.png)