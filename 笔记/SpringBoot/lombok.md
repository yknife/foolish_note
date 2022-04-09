1. 依赖

   ```xml
   <dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
   </dependency>
   ```

2. idea中搜索安装lombok插件

3. 例子

   ```java
   ===============================简化JavaBean开发===================================
   @NoArgsConstructor
   //@AllArgsConstructor
   @Data
   @ToString
   @EqualsAndHashCode
   public class User {
   
       private String name;
       private Integer age;
   
       private Pet pet;
   
       public User(String name,Integer age){
           this.name = name;
           this.age = age;
       }
   
   
   }
   
   
   
   ================================简化日志开发===================================
   @Slf4j
   @RestController
   public class HelloController {
       @RequestMapping("/hello")
       public String handle01(@RequestParam("name") String name){
           
           log.info("请求进来了....");
           
           return "Hello, Spring Boot 2!"+"你好："+name;
       }
   }
   ```

   