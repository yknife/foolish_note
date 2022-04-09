1. 打开配置

   ```yaml
   spring:
     mvc:
       hiddenmethod:
         filter:
           enabled: true
   ```

2. controller中使用Rest风格

   ```java
   @GetMapping("/user")
   public String getUser(){
       return "GET USER";
   }
   
   @PostMapping("/user")
   public String addUser(){
       return "POST USER";
   }
   
   @PutMapping("/user")
   public String updateUser(){
       return "PUT USER";
   }
   
   @DeleteMapping("/user")
   public String deleteUser(){
       return "DELETE USER";
   }
   ```

3. html中将真实method放入隐藏域中

   ```html
   <form action="/user" method="get">
       <input type="hidden" name="_method" value="GET"/>
       <input type="submit" value="GET提交"/>
   </form>
   <form action="/user" method="post">
       <input type="hidden" name="_method" value="POST"/>
       <input type="submit" value="POST提交"/>
   </form>
   <form action="/user" method="post">
       <input type="hidden" name="_method" value="PUT"/>
       <input type="submit" value="PUT提交"/>
   </form>
   <form action="/user" method="post">
       <input type="hidden" name="_method" value="DELETE"/>
       <input type="submit" value="DELETE提交"/>
   </form>
   ```

4. 原理

   Rest原理（表单提交要使用REST的时候）

   * 表单提交会带上_method=PUT

   * 请求过来被HiddenHttpMethodFilter拦截

   * 请求是否正常，并且是POST

   * 获取到_method的值。

   * 兼容以下请求；PUT.DELETE.PATCH

   * 原生request（post），包装模式requesWrapper重写了getMethod方法，返回的是传入的值。

   * 过滤器链放行的时候用wrapper。以后的方法调用getMethod是调用requesWrapper的。

   

   Rest使用客户端工具，

   * 如PostMan直接发送Put、delete等方式请求，无需Filter。

5. 修改默认_method

   ```java
   @Configuration(proxyBeanMethods = false)
   public class WebConfig {
       @Bean
       public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
           HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
           hiddenHttpMethodFilter.setMethodParam("_m");
           return hiddenHttpMethodFilter;
       }
   }
   ```