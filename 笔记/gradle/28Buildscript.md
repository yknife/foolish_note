## Buildscript

buildscript 里是gradle 脚本执行所需依赖，分别是对应的 maven 库和插件。案例如下：



import org.apache.commons.codec.binary.Base64 buildscript {

repositories {

mavenCentral()

}

dependencies {

classpath group: 'commons-codec', name: 'commons-codec', version: '1.2'

}

}

tasks.register('encode') { doLast {

def byte[] encodedString = new Base64().encode('hello world\n'.getBytes()) println new String(encodedString)

}

}

需要注意的是：

1. 1. 1. 1. buildscript{}必须在 build.gradle 文件的最前端。
         2. 对于多项目构建，项目的 buildscript ()方法声明的依赖关系可用于其所有子项目的构建脚本。
         3. 构建脚本依赖可能是 Gradle 插件。案例如下所示：







//老式apply插件的引用方式,使用apply+buildscript

buildscript { ext {

springBootVersion = "2.3.3.RELEASE"

}

repositories {

mavenLocal()

maven { url ['http://maven.aliyun.com/nexus/content/groups/public' ](http://maven.aliyun.com/nexus/content/groups/public')} jcenter()

}

//此处引入插件dependencies {

classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")

}

}

apply plugin: 'java' //核心插件，无需事先引入

apply plugin: 'org.springframework.boot' //社区插件，需要事先引入,才能应用，不必写版本号