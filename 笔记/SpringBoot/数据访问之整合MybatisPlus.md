1. 添加依赖

   ```xml
   <dependency>
     <groupId>com.baomidou</groupId>
     <artifactId>mybatis-plus-boot-starter</artifactId>
     <version>3.5.1</version>
   </dependency>
   ```

2. 自动配置

   - MybatisPlusAutoConfiguration 配置类，MybatisPlusProperties 配置项绑定。mybatis-plus：xxx 就是对mybatis-plus的定制
   - SqlSessionFactory 自动配置好。底层是容器中默认的数据源
   - mapperLocations 自动配置好的。有默认值。classpath\*:/mapper/\*\*/*.xml；任意包的类路径下的所有mapper文件夹下任意路径下的所有xml都是sql映射文件。  建议以后sql映射文件，放在 mapper下
   - 容器中也自动配置好了 SqlSessionTemplate
   - @Mapper 标注的接口也会被自动扫描；建议直接 @MapperScan("com.atguigu.admin.mapper") 批量扫描就行

3. Mapper接口

   ```java
   public interface UserMapper extends BaseMapper<User> {
   
   }
   ```

4. 实体

   ```java
   @Data
   @TableName("t_user")
   public class User {
   
       Integer id;
   
       String name;
   
       Integer age;
   
       Date createTime;
       @TableField(exist = false)
       String username;
       @TableField(exist = false)
       String password;
   
       public User(String username, String password) {
           this.username = username;
           this.password = password;
       }
   }
   ```

5. Mapper扫描

   ```java
   @SpringBootApplication
   @MapperScan("com.example.boot03.mapper")
   public class Boot03Application {
       public static void main(String[] args) {
           SpringApplication.run(Boot03Application.class, args);
       }
   }
   ```



