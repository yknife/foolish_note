## Gateway配置路由的两种方式

* **在配置文件yml中配置**

  ```yaml
  spring:
    application:
      name: cloud-gateway
    cloud:
      gateway:
        routes:
          - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
            uri: http://localhost:8001          #匹配后提供服务的路由地址
            #uri: lb://cloud-payment-service #匹配后提供服务的路由地址
            predicates:
              - Path=/payment/get/**         # 断言，路径相匹配的进行路由
  
          - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
            uri: http://localhost:8001          #匹配后提供服务的路由地址
            #uri: lb://cloud-payment-service #匹配后提供服务的路由地址
            predicates:
              - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
  ```

* **代码中注入RouteLocator的Bean**

  官方案例 - [link](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/#modifying-the-way-remote-addresses-are-resolved)

  ```java
  @Configuration
  public class GateWayConfig
  {
      @Bean
      public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder)
      {
          RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
  
          routes.route("path_route_atguigu",
                  r -> r.path("/guonei")
                          .uri("http://news.baidu.com/guonei")).build();
  
          return routes.build();
      }
  }
  ```

**测试**

浏览器输入http://localhost:9527/guonei，返回http://news.baidu.com/guonei相同的页面。