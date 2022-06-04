1. pom

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   ```

2. yaml

   ```yaml
   eureka:
     client:
       #表示是否将自己注册进Eurekaserver默认为true。
       register-with-eureka: true
       #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
       fetchRegistry: true
       service-url:
         defaultZone: http://localhost:7001/eureka
   ```

3. 启动类

   ```java
   @SpringBootApplication
   @EnableEurekaClient
   public class PaymentMain8001 {
   
       public static void main(String[] args) {
           SpringApplication.run(PaymentMain8001.class,args);
       }
   }
   ```