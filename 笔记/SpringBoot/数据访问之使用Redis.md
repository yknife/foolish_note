1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 配置连接

   ```yaml
   spring:
     redis:
       url: redis://localhost:6379
   ```

3. 自动配置：

   - RedisAutoConfiguration 自动配置类。RedisProperties 属性类 --> **spring.redis.xxx是对redis的配置**
   - 连接工厂是准备好的。**Lettuce**ConnectionConfiguration、**Jedis**ConnectionConfiguration
   - **自动注入了RedisTemplate**<**Object**, **Object**> ： xxxTemplate；
   - **自动注入了StringRedisTemplate；k：v都是String**
   - **key：value**
   - **底层只要我们使用** **StringRedisTemplate、**RedisTemplate就可以操作redis

4. 测试类

   ```java
   @Test
   void testRedis(){
       stringRedisTemplate.opsForValue().set("name","yknife");
       String name = stringRedisTemplate.opsForValue().get("name");
       System.out.println(name);
   }
   ```

5. 切换成jedis

   ```xml
   <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   <!--导入jedis-->
   <dependency>
     <groupId>redis.clients</groupId>
   <artifactId>jedis</artifactId>
   </dependency>
   ```

   ```yaml
   spring:
     redis:
         host: r-bp1nc7reqesxisgxpipd.redis.rds.aliyuncs.com
         port: 6379
         password: lfy:Lfy123456
         client-type: jedis
         jedis:
           pool:
             max-active: 10
   ```

   