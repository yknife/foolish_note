1. 编辑hive-site.xml

   ```xml
   <property>
   <!--解决beeline无法正常链接hiveserver2的问题-->
   <name>hive.server2.enable.doAs </name>
   <value>false</value>
   </property>
   ```

   