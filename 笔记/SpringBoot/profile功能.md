# Profile功能

为了方便多环境适配，springboot简化了profile功能。

## 1、application-profile功能

- 默认配置文件  application.yaml；任何时候都会加载
- 指定环境配置文件  application-{env}.yaml
- 激活指定环境

- - 配置文件激活
  - 命令行激活：java -jar xxx.jar --**spring.profiles.active=prod  --person.name=haha**

- - - **修改配置文件的任意值，命令行优先**

- 默认配置与环境配置同时生效
- 同名配置项，profile配置优先

## 2、@Profile条件装配功能

* 根据profile装配类

```java
@Configuration(proxyBeanMethods = false)
@Profile("production")
public class ProductionConfiguration {

    // ...

}
```

* 根据profile装配方法

```java
@Configuration(proxyBeanMethods = false)
public class MyConfig {

    @Bean
    @Profile("prod")
    public Color red(){
        return Color.red;
    }

    @Bean
    @Profile("dev")
    public Color green(){
        return Color.green;
    }

}
```

## 3、profile分组

```properties
spring.profiles.group.production[0]=proddb
spring.profiles.group.production[1]=prodmq

使用：--spring.profiles.active=production  激活
```

