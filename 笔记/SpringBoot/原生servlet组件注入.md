* 注入servlet

  ```java
  @WebServlet(urlPatterns = "/helloServlet")
  public class MyServlet extends HttpServlet {
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          resp.getWriter().write("hello servlet");
      }
  }
  ```

  ```java
  @ServletComponentScan(basePackages = "com.example.boot03")
  @SpringBootApplication
  public class Boot03Application {
  
      public static void main(String[] args) {
          SpringApplication.run(Boot03Application.class, args);
      }
  
  }
  ```

* 注入filter

  ```java
  @Slf4j
  @WebFilter(urlPatterns = {"/css/*","/images/*"})
  public class MyFilter implements Filter {
  
      @Override
      public void init(FilterConfig filterConfig) throws ServletException {
          log.info("MyFilter初始化");
      }
  
      @Override
      public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
          log.info("MyFilter工作");
          chain.doFilter(request,response);
      }
  
      @Override
      public void destroy() {
          log.info("MyFilter销毁");
      }
  }
  ```

* 注入listener

  ```java
  @Slf4j
  @WebListener
  public class MyListener implements ServletContextListener {
  
      @Override
      public void contextInitialized(ServletContextEvent sce) {
          log.info("项目初始化完成。");
      }
  
      @Override
      public void contextDestroyed(ServletContextEvent sce) {
          log.info("项目即将停止");
      }
  }
  ```

* 使用RegistrationBean方式注入

  ```java
  @Configuration(proxyBeanMethods = true)
  public class MyServletConfig {
  
      @Bean
      public ServletRegistrationBean<MyServlet> myServlet(){
          MyServlet myServlet = new MyServlet();
          return new ServletRegistrationBean<>(myServlet,"/helloServlet");
      }
  
      @Bean
      public FilterRegistrationBean<MyFilter> myFilter(){
          MyFilter myFilter = new MyFilter();
  //        return new FilterRegistrationBean<>(myFilter,myServlet());
          FilterRegistrationBean<MyFilter> registrationBean = new FilterRegistrationBean<>(myFilter);
          registrationBean.setUrlPatterns(Arrays.asList("/css/*","/images/*"));
          return registrationBean;
      }
  
      @Bean
      public ServletListenerRegistrationBean<MyListener> myListener(){
          MyListener myListener = new MyListener();
          return new ServletListenerRegistrationBean<>(myListener);
      }
  
  }
  ```

* 扩展：DispatchServlet 如何注册进来

  - 容器中自动配置了  DispatcherServlet  属性绑定到 WebMvcProperties；对应的配置文件配置项是 **spring.mvc。**
  - **通过** **ServletRegistrationBean**<DispatcherServlet> 把 DispatcherServlet  配置进来。
  - 默认映射的是 / 路径。

  ![img](原生servlet组件注入.assets/1606284869220-8b63d54b-39c4-40f6-b226-f5f095ef9304.png)

  Tomcat-Servlet；

  多个Servlet都能处理到同一层路径，精确优选原则

  A： /my/

  B： /my/1









filter用于字符过滤，listener用于初始化作用域数据，interceptor用于拦截请求