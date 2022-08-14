## Sentinel是什么

[官方Github](https://github.com/alibaba/Sentinel)

[官方文档](https://sentinelguard.io/zh-cn/docs/introduction.html)

> Sentinel 是什么？
>
> 随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。
>
> Sentinel 具有以下特征:
>
> * 丰富的应用场景：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
> * 完备的实时监控：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
> * 广泛的开源生态：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
> * 完善的 SPI 扩展点：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。
>
> Sentinel 的主要特性：
>
> ![img](Sentinel介绍.assets/e4efa9c3547366ae4f747ad4007f6447.png)
>
> [link](https://github.com/alibaba/Sentinel/wiki/介绍#sentinel-是什么)

—句话解释，之前我们讲解过的Hystrix。

Hystrix与Sentinel比较：

Hystrix

* 需要我们程序员自己手工搭建监控平台
* 没有一套web界面可以给我们进行更加细粒度化得配置流控、速率控制、服务熔断、服务降级

Sentinel

* 单独一个组件，可以独立出来。
* 直接界面化的细粒度统一配置。
* 约定 > 配置 > 编码

都可以写在代码里面，但是我们本次还是大规模的学习使用配置和注解的方式，尽量少写代码

> sentinel
> 英 [ˈsentɪnl] 美 [ˈsentɪnl]
> n. 哨兵