1. 添加druid-starter依赖

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid-spring-boot-starter</artifactId>
       <version>1.2.9</version>
   </dependency>
   ```

2. 分析自动配置

   - 扩展配置项 **spring.datasource.druid**
   - DruidSpringAopConfiguration.**class**,   监控SpringBean的；配置项：**spring.datasource.druid.aop-patterns**
   - DruidStatViewServletConfiguration.**class**, 监控页的配置：**spring.datasource.druid.stat-view-servlet；默认开启**
   -  DruidWebStatFilterConfiguration.**class**, web监控配置；**spring.datasource.druid.web-stat-filter；默认开启**
   - DruidFilterConfiguration.**class**}) 所有Druid自己filter的配置

   ```java
   private static final String FILTER_STAT_PREFIX = "spring.datasource.druid.filter.stat";
   private static final String FILTER_CONFIG_PREFIX = "spring.datasource.druid.filter.config";
   private static final String FILTER_ENCODING_PREFIX = "spring.datasource.druid.filter.encoding";
   private static final String FILTER_SLF4J_PREFIX = "spring.datasource.druid.filter.slf4j";
   private static final String FILTER_LOG4J_PREFIX = "spring.datasource.druid.filter.log4j";
   private static final String FILTER_LOG4J2_PREFIX = "spring.datasource.druid.filter.log4j2";
   private static final String FILTER_COMMONS_LOG_PREFIX = "spring.datasource.druid.filter.commons-log";
   private static final String FILTER_WALL_PREFIX = "spring.datasource.druid.filter.wall";
   ```

3. 配置yaml

   ```yaml
   spring:
     datasource:
       driver-class-name: com.mysql.jdbc.Driver
       url: jdbc:mysql://localhost:3306/mybatis
       username: root
       password: 1
       druid:
         filters: stat,wall
         stat-view-servlet:
           enabled: true
           login-username: admin
           login-password: password
         web-stat-filter:
           enabled: true
           exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
           url-pattern: /*
         filter:
           stat:
             slow-sql-millis: 1000
             log-slow-sql: true
             enabled: true
           wall:
             config:
               drop-table-allow: false
             enabled: true
         aop-patterns: com.example.boot03.* # 开启spring监控支持，需要配合spring.aop.auto=false使用
     aop:
       auto: false
   ```