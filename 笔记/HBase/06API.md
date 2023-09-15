1. 添加依赖

   ```xml
   <dependencies>
    <dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-server</artifactId>
    <version>2.4.11</version>
    <exclusions>
    <exclusion>
    <groupId>org.glassfish</groupId>
    <artifactId>javax.el</artifactId>
    </exclusion>
    </exclusions>
    </dependency>
    <dependency>
    <groupId>org.glassfish</groupId>
    <artifactId>javax.el</artifactId>
    <version>3.0.1-b06</version>
    </dependency>
   </dependencies>
   ```

2. 创建一次性连接

   ```java
   public static void main(String[] args) throws IOException {
       // 1. 创建配置对象
       Configuration conf = new Configuration();
       // 2. 添加配置参数
       conf.set("hbase.zookeeper.quorum","hadoop01,hadoop02,hadoop03"
       );
       // 3. 创建 hbase 的连接
       // 默认使用同步连接
       Connection connection =
               ConnectionFactory.createConnection(conf);
       // 4. 使用连接
       System.out.println(connection);
       // 5. 关闭连接
       connection.close();
   }
   ```

3. 创建静态连接工具

   ```java
   public class MyConnection {
   
   
       // 设置静态属性 hbase 连接
       public static Connection connection = null;
       static {
           // 创建 hbase 的连接
           try {
               // 使用配置文件的方法
               connection = ConnectionFactory.createConnection();
           } catch (IOException e) {
               System.out.println("连接获取失败");
               e.printStackTrace();
           }
       }
   
       /**
        * 连接关闭方法,用于进程关闭时调用
        * @throws IOException
        */
       public static void closeConnection() throws IOException {
           if (connection != null) {
               connection.close();
           }
       }
   }
   ```

   需要同时配置hbase-site.xml

   ```xml
   <?xml version="1.0"?>
   <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
   <configuration>
       <property>
           <name>hbase.zookeeper.quorum</name>
           <value>hadoop01,hadoop02,hadoop03</value>
       </property>
   </configuration>
   ```

4. 如何使用

   ```java
   public class HBaseDDL {
       // 添加静态属性 connection 指向单例连接
       public static Connection connection = MyConnection.connection;
   }
   ```

   