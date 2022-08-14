## Sentinel下载安装运行

[官方文档](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_sentinel)

服务使用中的各种问题：

* 服务雪崩
* 服务降级
* 服务熔断
* 服务限流

Sentinel 分为两个部分：

* 核心库（Java 客户端）不依赖任何框架/库，能够运行于所有 Java 运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
* 控制台（Dashboard）基于 Spring Boot 开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。
  安装步骤：

下载

* https://github.com/alibaba/Sentinel/releases
* 下载到本地sentinel-dashboard-1.7.0.jar

运行命令

* 前提
  * Java 8 环境
  * 8080端口不能被占用

* 命令
  * java -jar sentinel-dashboard-1.7.0.jar

访问Sentinel管理界面

* localhost:8080
* 登录账号密码均为sentinel
  