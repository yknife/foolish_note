### [spring-data-jpa-extra](https://github.com/slyak/spring-data-jpa-extra)

> 作者：I love spring-data-jpa, she set my hands free, crud methods are boring! However she is not perfect on dynamic native query and her return type must be an entity, although she provide us a specification solution, but i think it's heavy and not easy to use.

> **spring-data-jpa-extra comes to solve three problem:**
>
> * dynamic native query support like mybatis
> * return type can be anything
> * no code, just sql

1. 引入依赖

   ```xml
   <dependency>
     <groupId>com.slyak</groupId>
     <artifactId>spring-boot-starter-jpa-extra</artifactId>
     <version>3.3.0.RELEASE</version>
   </dependency>
   ```

2. 配置SpringBoot启动类

   ```java
   @SpringBootApplication
   @EnableJpaRepositories( repositoryBaseClass = GenericJpaRepositoryImpl.class, repositoryFactoryBeanClass = GenericJpaRepositoryFactoryBean.class)
   @ComponentScan({"com.slyak","com.example.boot02"})
   public class Boot02Application {
       public static void main(String[] args) {
           SpringApplication.run(Boot02Application.class, args);
       }
   }
   ```

3. 创建dao

   ```java
   public interface UserDao extends GenericJpaRepository<User,Long> {
       @TemplateQuery
       Page<User> fetchBySql(Pageable pageable,@Param("age") Long age);
   }
   ```

4. 创建模板。在/sqls下创建User.sftl模板

   ```sql
   --fetchBySql
   select
       *
   from t_user
   where
       1=1
   <#if age??>
       and age = #{age}
   </#if>
   ```