### Repositories

repositories {

//gradle中会按着仓库配置的顺序，从上往下依次去对应的仓库中找所需要的jar包:

//如果找到，则停止向下搜索，如果找不到，继续在下面的仓库中查找

//指定去本地某个磁盘目录中查找:使用本地file文件协议:一般不用这种方式

maven { url 'file:///D:/repos/mavenrepos3.5.4'} maven { url "$rootDir/lib/release" }

//指定去maven的本地仓库查找

mavenLocal()

//指定去maven的私服或者第三方镜像仓库查找

maven { name "Alibaba" ; url "https://maven.aliyun.com/repository/public" } maven { name "Bstek" ; url "https://nexus.bsdn.org/content/groups/public/" }

//指定去maven的远程仓库查找:即 https://repo.maven.apache.org/maven2/

mavenCentral()

//去google仓库查找google()

}

因为 Gradle 没有自己的远程仓库，而是使用 Maven、jcenter、jvy、google 这些远程仓库。