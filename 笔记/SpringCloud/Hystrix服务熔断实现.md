1. 熔断fallback配置

   ```java
   @Service
   public class PaymentService {
   
       //=====服务熔断
       @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
               @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),// 是否开启断路器
               @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),// 请求次数
               @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"), // 时间窗口期
               @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"),// 失败率达到多少后跳闸
       })
       public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
           if(id < 0) {
               throw new RuntimeException("******id 不能负数");
           }
           String serialNumber = IdUtil.simpleUUID();
   
           return Thread.currentThread().getName()+"\t"+"调用成功，流水号: " + serialNumber;
       }
       public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
           return "id 不能负数，请稍后再试，/(ㄒoㄒ)/~~   id: " +id;
       }
   }
   ```

2. 控制类

   ```java
   @RestController
   @Slf4j
   public class PaymentController
   {
       @Resource
       private PaymentService paymentService;
   
       @Value("${server.port}")
       private String serverPort;
   
       //====服务熔断
       @GetMapping("/payment/circuit/{id}")
       public String paymentCircuitBreaker(@PathVariable("id") Integer id)
       {
           String result = paymentService.paymentCircuitBreaker(id);
           log.info("****result: "+result);
           return result;
       }
   }
   ```

3. 测试

   自测cloud-provider-hystrix-payment8001

   正确 - http://localhost:8001/payment/circuit/1

   错误 - http://localhost:8001/payment/circuit/-1

   多次错误，再来次正确，但错误得显示

   重点测试 - 多次错误，然后慢慢正确，发现刚开始不满足条件，就算是正确的访问地址也不能进行