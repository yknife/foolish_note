1. 修改yaml

   ```yaml
   server:
     port: 80
   
   eureka:
     client:
       register-with-eureka: false
       service-url:
         defaultZone: http://eureka7001.com:7001/eureka/
   
   #开启
   feign:
     hystrix:
       enabled: true
   ```

   > feign.hystrix.enabled=true是开启feign对Hystrix的支持，可以直接在@FeignClent注解中的fallback属性指定回调的类

2. 主启动类添加注解

   ```java
   @SpringBootApplication
   @EnableFeignClients
   @EnableHystrix//添加到此处
   public class OrderHystrixMain80
   {
       public static void main(String[] args)
       {
           SpringApplication.run(OrderHystrixMain80.class,args);
       }
   }
   ```

   > @EnableHystrix继承了@EnableCircuitBreaker



写在controller类内部的处理，并加上默认或者指定降级处理。**如果存在一个继承service的降级处理，他处理的是客户端自身的异常**