1. 添加maven依赖

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid</artifactId>
       <version>1.1.17</version>
   </dependency>
   ```

2. @Configuration方式配置druid数据源

   ```java
   @Configuration
   public class MyDataSourceConfig {
   
       @Bean
       @ConfigurationProperties("spring.datasource")
       public DataSource dataSource(){
           DruidDataSource druidDataSource = new DruidDataSource();
           return druidDataSource;
       }
   
   }
   ```

3. 以servlet的方式配置druid的监控页,StatViewServlet

   StatViewServlet的用途包括：

   - 提供监控信息展示的html页面
   - 提供监控信息的JSON API

   ```xml
   <servlet>
     <servlet-name>DruidStatView</servlet-name>
     <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
   </servlet>
   <servlet-mapping>
     <servlet-name>DruidStatView</servlet-name>
     <url-pattern>/druid/*</url-pattern>
   </servlet-mapping>
   ```

   代码方式实现：

   ```java
   @Bean
   @ConfigurationProperties("spring.datasource")
   public DataSource dataSource() throws SQLException {
     DruidDataSource druidDataSource = new DruidDataSource();
     druidDataSource.setFilters("stat,wall"); //stat:开启统计功能，wall:开启防火墙功能
     return druidDataSource;
   }
   
   @Bean
   public ServletRegistrationBean<StatViewServlet> statViewServlet(){
       StatViewServlet servlet = new StatViewServlet();
       ServletRegistrationBean<StatViewServlet> registrationBean
               = new ServletRegistrationBean<>(servlet, "/druid/*");//配置servlet的urlMapping地址，虽然页面可以访问，但是没有真正的开启统计功能，需要配合设置druidDataSource.setFilters("stat")，统计功能才可以生效
       registrationBean.addInitParameter("loginUsername","admin");//设置druid监控页面的账号密码
       registrationBean.addInitParameter("loginPassword","123");
       return registrationBean;
   }
   
   //开启web统计功能
   @Bean
   public FilterRegistrationBean<WebStatFilter> webStatFilter(){
     WebStatFilter webStatFilter = new WebStatFilter();
     FilterRegistrationBean<WebStatFilter> filterFilterRegistrationBean = new FilterRegistrationBean<>(webStatFilter);
    filterFilterRegistrationBean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");//设置不需要统计的请求
     return filterFilterRegistrationBean;
   }
   ```

   

   

   

   