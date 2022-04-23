## 快速整合

1. 添加依赖

   ```xml
   <dependency>
       <groupId>org.mybatis.spring.boot</groupId>
       <artifactId>mybatis-spring-boot-starter</artifactId>
       <version>2.2.2</version>
   </dependency>
   ```

2. yaml配置

   ```yaml
   mybatis:
     mapper-locations: classpath:mybatis/mappers/*.xml
     configuration:
       map-underscore-to-camel-case: true
   ```

3. 编写Mapper接口

   ```java
   public interface UserMapper {
       List<User> findAll();
   }
   
   //注解模式
   @Mapper
   public interface CityMapper {
   
       @Select("select * from city where id=#{id}")
       public City getById(Long id);
   
       public void insert(City city);
   
   }
   ```

4. 编写Mapper的xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.example.boot03.mapper.UserMapper">
       <select id="findAll" resultType="com.example.boot03.entity.User">
           select * from t_user
       </select>
   </mapper>
   ```

5. 启动类中添加mapper扫描注解

   ```java
   @ServletComponentScan(basePackages = "com.example.boot03")
   @SpringBootApplication
   @MapperScan("com.example.boot03.mapper")
   public class Boot03Application {
       public static void main(String[] args) {
           SpringApplication.run(Boot03Application.class, args);
       }
   }
   ```