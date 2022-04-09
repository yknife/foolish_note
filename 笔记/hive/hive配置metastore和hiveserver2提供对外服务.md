1. 编辑hive-site.xml

   ```xml
    <!-- 指定存储元数据要连接的地址 -->
    <property>
    <name>hive.metastore.uris</name>
    <value>thrift://node01:9083</value>
    </property> 
   
    <!-- 指定 hiveserver2 连接的 host -->
     <property>
    <name>hive.server2.thrift.bind.host</name>
    <value>node01</value>
    </property> 
    <!-- 指定 hiveserver2 连接的端口号 -->
    <property>
    <name>hive.server2.thrift.port</name>
    <value>10000</value>
    </property> 
   ```

   