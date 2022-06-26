**目前问题2** 统一和自定义的分开，代码混乱

**服务降级，客户端去调用服务端，碰上服务端宕机或关闭**

本次案例服务降级处理是在客户端80实现完成的，与服务端8001没有关系，只需要为[Feign](https://so.csdn.net/so/search?q=Feign&spm=1001.2101.3001.7020)客户端定义的接口添加一个服务降级处理的实现类即可实现解耦

**未来我们要面对的异常**

- 运行
- 超时
- 宕机

1. 重新新建一个类(AaymentFallbackService)实现该接口，统一为接口里面的方法进行异常处理

   PaymentFallbackService类实现PaymentHystrixService接口

   ```java
   @Component
   public class PaymentFallbackService implements PaymentHystrixService
   {
       @Override
       public String paymentInfo_OK(Integer id)
       {
           return "-----PaymentFallbackService fall back-paymentInfo_OK ,o(╥﹏╥)o";
       }
   
       @Override
       public String paymentInfo_TimeOut(Integer id)
       {
           return "-----PaymentFallbackService fall back-paymentInfo_TimeOut ,o(╥﹏╥)o";
       }
   }
   ```

   ```java
   @Component
   @FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT" ,fallback = PaymentFallbackService.class)//指定PaymentFallbackService类
   public interface PaymentHystrixService
   {
       @GetMapping("/payment/hystrix/ok/{id}")
       public String paymentInfo_OK(@PathVariable("id") Integer id);
   
       @GetMapping("/payment/hystrix/timeout/{id}")
       public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
   }
   ```

2. 配置feign.hystrix开启

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

3. 测试

   单个eureka先启动7001

   PaymentHystrixMain8001启动

   正常访问测试 - http://localhost/consumer/payment/hystrix/ok/1

   故意关闭微服务8001

   客户端自己调用提示 - 此时服务端provider已经down了，但是我们做了服务降级处理，让客户端在服务端不可用时也会获得提示信息而不会挂起耗死服务器。