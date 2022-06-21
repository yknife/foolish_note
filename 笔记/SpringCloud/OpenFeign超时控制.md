1. 服务提供方8001/8002故意写暂停程序

   ```java
   @RestController
   @Slf4j
   public class PaymentController {
       
       ...
       
       @Value("${server.port}")
       private String serverPort;
   
       ...
       
       @GetMapping(value = "/payment/feign/timeout")
       public String paymentFeignTimeout()
       {
           // 业务逻辑处理正确，但是需要耗费3秒钟
           try {
               TimeUnit.SECONDS.sleep(3);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return serverPort;
       }
       
       ...
   }
   ```

2. 服务消费方80添加超时方法PaymentFeignService

   ```java
   @Component
   @FeignClient(value = "CLOUD-PAYMENT-SERVICE")
   public interface PaymentFeignService{
   
       ...
   
       @GetMapping(value = "/payment/feign/timeout")
       public String paymentFeignTimeout();
   }
   ```

3. 服务消费方80添加超时方法OrderFeignController

   ```java
   @RestController
   @Slf4j
   public class OrderFeignController
   {
       @Resource
       private PaymentFeignService paymentFeignService;
   
       ...
   
       @GetMapping(value = "/consumer/payment/feign/timeout")
       public String paymentFeignTimeout()
       {
           // OpenFeign客户端一般默认等待1秒钟
           return paymentFeignService.paymentFeignTimeout();
       }
   }
   ```

4. 默认配置会报读超时

   将会跳出错误Spring Boot默认错误页面，主要异常：`feign.RetryableException:Read timed out executing GET http://CLOUD-PAYMENT-SERVCE/payment/feign/timeout`。

   **OpenFeign默认等待1秒钟，超过后报错**

5. yaml文件中超时配置

   ```yaml
   #设置feign客户端超时时间(OpenFeign默认支持ribbon)(单位：毫秒)
   ribbon:
     #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
     ReadTimeout: 5000
     #指的是建立连接后从服务器读取到可用资源所用的时间
     ConnectTimeout: 5000
   ```

   