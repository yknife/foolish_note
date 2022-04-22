## 定制Servlet容器

- 实现  WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> 

  - 把配置文件的值和ServletWebServerFactory 进行绑定

    ```java
    @Component
    public class MyWebServerFactoryCustomizer implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {
    
        @Override
        public void customize(ConfigurableServletWebServerFactory factory) {
            factory.setPort(9200);
        }
    }
    ```

  - xxxxxCustomizer：定制化器，可以改变xxxx的默认规则

- 修改配置文件 server.xxx

  ```yaml
  server:
    port: 8888
  ```

- 直接自定义 ConfigurableServletWebServerFactory

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
  
      @Bean
      public ConfigurableServletWebServerFactory webServerFactory(){
          UndertowServletWebServerFactory webServerFactory = new UndertowServletWebServerFactory();
          webServerFactory.setPort(9000);
          return webServerFactory;
      }
  }
  ```
