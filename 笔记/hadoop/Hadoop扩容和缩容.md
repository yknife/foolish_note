1. 克隆虚拟机

2. 配置ip和hostname

3. namenode配置免密登陆新节点

4. resourceManager配置免密登陆新节点

5. 拷贝环境变量

6. 拷贝hadoop

7. 删除hadoop目录中的data和logs目录

8. 单点启动hdfs

   ```shell
   hdfs --daemon start datanode
   ```

9. 单点启动yarn

   ```sh
   yarn --daemon start nodemanager

10. 尝试上传一个文件到hdfs

    ```shell
    hadoop fs -put xxx /
    ```

11. 使用黑名单方式退役服务器

12. 在HADOOP_HOME/etc/hadoop目录下创建whitelist和blacklist

13. 编辑blacklist，添加需要退役的服务器

    ```sh
    node04
    ```

14. 修改hdfs-site.xml

    ```xml
    <!-- 白名单 -->
    <property>
         <name>dfs.hosts</name>
         <value>/usr/local/hadoop-3.3.1/etc/hadoop/whitelist</value>
    </property>
    
    <!-- 黑名单 -->
    <property>
         <name>dfs.hosts.exclude</name>
         <value>/usr/local/hadoop-3.3.1/etc/hadoop/blacklist</value>
    </property>
    ```

15. 第一次配置黑白名单时，需要重启整个集群

    ```shell
    switch_cluster.sh stop
    ```

16. 后续调整黑白名单时可使用命令动态变更

    ```sh
    hdfs dfsadmin -refreshNodes
    ```

17. 手动关闭退役的服务器上的datanode和nodemanager服务

    ```sh
    hdfs --daemon stop datanode
    yarn --daemon stop nodemanager
    ```

    