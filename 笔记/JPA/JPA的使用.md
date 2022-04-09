1. maven增加引用

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
   </dependency>
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.17</version>
   </dependency>
   ```

2. pg版本

   ```xml
   <dependency>
       <groupId>org.postgresql</groupId>
       <artifactId>postgresql</artifactId>
   </dependency>
   ```

3. 新增实体

   ```java
   package com.example.boot.entity;
   
   import org.hibernate.annotations.GeneratorType;
   
   import javax.persistence.*;
   import java.util.Date;
   
   @Entity
   @Table(name = "t_user")
   public class User {
   
       @Id
       @GeneratedValue(strategy= GenerationType.AUTO)//适用于mysql
       Integer id;
   
       String name;
   
       Integer age;
   
       Date createTime;
   
       public Integer getId() {
           return id;
       }
   
       public void setId(Integer id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public Integer getAge() {
           return age;
       }
   
       public void setAge(Integer age) {
           this.age = age;
       }
   
       public Date getCreateTime() {
           return createTime;
       }
   
       public void setCreateTime(Date createTime) {
           this.createTime = createTime;
       }
   }
   ```

4. 增加dao

   ```java
   package com.example.boot.dao;
   
   import com.example.boot.entity.User;
   import org.springframework.data.repository.CrudRepository;
   
   public interface UserDao extends CrudRepository<User,Integer> {
   }
   ```

5. 新增application.properties中数据库相关配置

   ```properties
   spring.datasource.url = jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8
   spring.datasource.username = root
   spring.datasource.password = 1
   spring.datasource.driverClassName = com.mysql.jdbc.Driver
   ```

6. pg数据库版本

   ```properties
   spring.datasource.url=jdbc:postgresql://localhost:5432/user_db
   spring.datasource.username = postgres
   spring.datasource.password = 1
   spring.datasource.driverClassName = org.postgresql.Driver
   ```

   