1. 7001/7002集群启动

2. 8001/8002微服务改造- controller

   ```java
   @RestController
   @Slf4j
   public class PaymentController{
   
       ...
       
   	@GetMapping(value = "/payment/lb")
       public String getPaymentLB() {
           return serverPort;//返回服务接口
       }
       
       ...
   }
   ```

3. 80订单微服务改造

   * ApplicationContextConfig去掉注解@LoadBalanced，OrderMain80去掉注解@RibbonClient

     ```java
     import org.springframework.cloud.client.loadbalancer.LoadBalanced;
     import org.springframework.context.annotation.Bean;
     import org.springframework.context.annotation.Configuration;
     import org.springframework.web.client.RestTemplate;
     
     @Configuration
     public class ApplicationContextConfig {
     
         @Bean
         //@LoadBalanced
         public RestTemplate getRestTemplate(){
             return new RestTemplate();
         }
     }
     ```

   * 创建LoadBalancer接口

     ```java
     import org.springframework.cloud.client.ServiceInstance;
     
     import java.util.List;
     
     /**
      */
     public interface LoadBalancer
     {
         ServiceInstance instances(List<ServiceInstance> serviceInstances);
     }
     ```

   * MyLB

     ```java
     import org.springframework.cloud.client.ServiceInstance;
     import org.springframework.stereotype.Component;
     
     import java.util.List;
     import java.util.concurrent.atomic.AtomicInteger;
     
     /**
      */
     @Component//需要跟主启动类同包，或者在其子孙包下。
     public class MyLB implements LoadBalancer
     {
     
         private AtomicInteger atomicInteger = new AtomicInteger(0);
     
         public final int getAndIncrement()
         {
             int current;
             int next;
     
             do {
                 current = this.atomicInteger.get();
                 next = current >= 2147483647 ? 0 : current + 1;
             }while(!this.atomicInteger.compareAndSet(current,next));
             System.out.println("*****第几次访问，次数next: "+next);
             return next;
         }
     
         //负载均衡算法：rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标  ，每次服务重启动后rest接口计数从1开始。
         @Override
         public ServiceInstance instances(List<ServiceInstance> serviceInstances)
         {
             int index = getAndIncrement() % serviceInstances.size();
     
             return serviceInstances.get(index);
         }
     }
     ```

   * OrderController

     ```java
     import org.springframework.cloud.client.ServiceInstance;
     import org.springframework.cloud.client.discovery.DiscoveryClient;
     import com.lun.springcloud.lb.LoadBalancer;
     
     @Slf4j
     @RestController
     public class OrderController {
     
         //public static final String PAYMENT_URL = "http://localhost:8001";
         public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";
     
     	...
     
         @Resource
         private LoadBalancer loadBalancer;
     
         @Resource
         private DiscoveryClient discoveryClient;
     
     	...
     
         @GetMapping(value = "/consumer/payment/lb")
         public String getPaymentLB()
         {
             List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
     
             if(instances == null || instances.size() <= 0){
                 return null;
             }
     
             ServiceInstance serviceInstance = loadBalancer.instances(instances);
             URI uri = serviceInstance.getUri();
     
             return restTemplate.getForObject(uri+"/payment/lb",String.class);
     
         }
     }
     ```

     