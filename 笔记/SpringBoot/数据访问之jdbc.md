## 数据访问

1. maven中添加jdbc依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jdbc</artifactId>
   </dependency>
   ```

2. 配置文件配置数据源

   ```yaml
   spring:
     datasource:
       driver-class-name: com.mysql.jdbc.Driver
       url: jdbc:mysql://localhost:3306/mybatis
       username: root
       password: 1
       type: com.zaxxer.hikari.HikariDataSource
   ```

3. 驱动配置

   ```xml
   默认版本：<mysql.version>8.0.22</mysql.version>
   
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
   <!--            <version>5.1.49</version>-->
           </dependency>
   想要修改版本
   1、直接依赖引入具体版本（maven的就近依赖原则）
   2、重新声明版本（maven的属性的就近优先原则）
       <properties>
           <java.version>1.8</java.version>
           <mysql.version>5.1.49</mysql.version>
       </properties>
   ```