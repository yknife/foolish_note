## 为什么叫Nacos

前四个字母分别为Naming和Configuration的前两个字母，最后的s为Service。
## 是什么

一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

* Nacos: Dynamic Naming and Configuration Service
* Nacos就是注册中心＋配置中心的组合 -> Nacos = Eureka+Config+Bus

## 能干嘛

* 替代Eureka做服务注册中心
* 替代Config做服务配置中心

## 去哪下

https://github.com/alibaba/nacos/releases
[官网文档](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring cloud alibaba nacos_discovery)

## 各中注册中心比较

| 服务注册与发现框架 | CAP模型 | 控制台管理 | 社区活跃度      |
| ------------------ | ------- | ---------- | --------------- |
| Eureka             | AP      | 支持       | 低(2.x版本闭源) |
| Zookeeper          | CP      | 不支持     | 中              |
| consul             | CP      | 支持       | 高              |
| Nacos              | AP      | 支持       | 高              |

据说Nacos在阿里巴巴内部有超过10万的实例运行，已经过了类似双十一等各种大型流量的考验。