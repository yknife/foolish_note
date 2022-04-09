1. insert

   ```java
   import mappers.UserMapper;
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.Test;
   
   import java.io.IOException;
   import java.io.InputStream;
   
   public class CURDTest {
   
       @Test
       public void insert() throws IOException {
           InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
           SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           //SqlSession sqlSession = sqlSessionFactory.openSession();
           SqlSession sqlSession = sqlSessionFactory.openSession(true);//autocommit
           UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
           userMapper.insertUser();
           //sqlSession.commit();
       }
   }
   ```

   ```xml
   <insert id="insertUser" >
     insert into t_user values (null,'yknife',24,'2022-03-11 00:00:00')
   </insert>
   ```

   ```java
   public interface UserMapper {
   
       int insertUser();
   
       int deleteUser();
   
       int updateUser();
   
       List<User> selectUsers();
   
   }
   ```

2. delete

   ```xml
   <delete id="deleteUser">
     delete from t_user where id = 1
   </delete>
   ```

3. update

   ```xml
   <update id="updateUser">
     update t_user set name = 'leo' where id = 2
   </update>
   ```

4. select

   ```xml
   <select id="selectUsers" resultType="entity.User">
     select * from t_user
   </select>
   ```

5. UserMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="mappers.UserMapper"> 
       <insert id="insertUser" >
           insert into t_user values (null,'yknife',24,'2022-03-11 00:00:00')
     </insert>
       <delete id="deleteUser">
           delete from t_user where id = 1
       </delete>
       <update id="updateUser">
           update t_user set name = 'leo' where id = 2
       </update>
       <select id="selectUsers" resultType="entity.User">
           select * from t_user
       </select>
   
   </mapper>
   ```

   两个一致：1.namespace与接口全类名保持一致。2.id与接口的方法名保持一致。

6. 返回类型resultMap和resultType的区别

   > 查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射关系  
   > 	- resultType：自动映射，用于属性名和表中字段名一致的情况  
   > 	- resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况 