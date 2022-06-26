8001先从自身找问题

**设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处埋，作服务降级fallback**。

**8001fallback**

业务类启用 - @HystrixCommand报异常后如何处理

1. —旦调用服务方法失败并抛出了错误信息后，会自动调用@HystrixCommand标注好的fallbackMethod调用类中的指定方法

   ```java
   @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler"/*指定善后方法名*/,commandProperties = {
               @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="3000")
       })
   public String paymentInfo_TimeOut(Integer id)
   {
     //当遇到异常和超时都会交由善后方法降级处理
     int n = 3/0;
     //        try { TimeUnit.MILLISECONDS.sleep(5000); } catch (InterruptedException e) { e.printStackTrace(); }
     return "线程池:  "+Thread.currentThread().getName()+" id:  "+id+"\t"+"O(∩_∩)O哈哈~"+"  耗时(秒): 3";
   }
   ```

2. 善后方法

   ```java
   //用来善后的方法
   public String paymentInfo_TimeOutHandler(Integer id)
   {
       return "线程池:  "+Thread.currentThread().getName()+"  8001系统繁忙或者运行报错，请稍后再试,id:  "+id+"\t"+"o(╥﹏╥)o";
   }
   ```

3. 主启动类添加注解@EnableCircuitBreaker

   ```java
   @SpringBootApplication
   @EnableEurekaClient
   @EnableCircuitBreaker//添加到此处
   public class PaymentHystrixMain8001
   {
       public static void main(String[] args) {
           SpringApplication.run(PaymentHystrixMain8001.class, args);
       }
   }
   ```

