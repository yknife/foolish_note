# 自定义starter

## 1、starter启动原理

- starter-pom引入 autoconfigurer 包

![img](自定义starter.assets/1606995919308-b2c7ccaa-e720-4cc5-9801-2e170b3102e1.png)

- autoconfigure包中配置使用 **META-INF/spring.factories** 中 **EnableAutoConfiguration 的值，使得项目启动加载指定的自动配置类**

  ```factories
  # Auto Configure
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.example.hello.config.HelloAutoConfiguration
  ```

- **编写自动配置类 xxxAutoConfiguration -> xxxxProperties**

- - **@Configuration**
  - **@Conditional**
  - **@EnableConfigurationProperties**
  - **@Bean**
  - ......

**引入starter** **--- xxxAutoConfiguration --- 容器中放入组件 ---- 绑定xxxProperties ----** **配置项**

## 2、自定义starter

**atguigu-hello-spring-boot-starter（启动器）**

**atguigu-hello-spring-boot-starter-autoconfigure（自动配置包）**

## 3、代码

```java
//配置文件设置
@ConfigurationProperties("example.hello")
public class HelloProperties {

    String prefix;
    String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}
```

```java
//无需在此处注入到容器中
public class HelloService {

    @Autowired
    HelloProperties helloProperties;

    public String sayHello(String name){
        return helloProperties.getPrefix()+":"+name+"》"+helloProperties.getSuffix();
    }
}
```

```java
@Configuration
//启用配置文件对象
@EnableConfigurationProperties(HelloProperties.class)
public class HelloAutoConfiguration {

    @Bean
    //当容器中不存在时执行
    @ConditionalOnMissingBean(HelloService.class)
    public HelloService helloService(){
        HelloService helloService = new HelloService();
        return helloService;
    }
}
```