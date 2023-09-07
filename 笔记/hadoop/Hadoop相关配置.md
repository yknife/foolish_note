1. 设置IP

   ```shell
   su root
   vim /etc/sysconfig/network-scripts/ifcfg-ens33
   //编辑后保存
   BOOTPROTO="static"
   IPADDR=192.168.225.10
   GATEWAY=192.168.225.2
   DNS1=192.168.225.2
   ```

2. 设置主机名

   ```shell
   vim /etc/hostname
   //编辑后保存
   node00
   ```

3. 设置主机IP映射

   ```shell
   vim /etc/hosts
   //编辑后保存
   192.168.225.10 node00
   192.168.225.11 node01
   192.168.225.12 node02
   192.168.225.13 node03
   ```

4. 配置hadoop环境变量

   ```shell
   vim /etc/profile.d/my_env.sh
   #HADOOP_HOME
   export HADOOP_HOME=/usr/local/hadoop-3.3.1
   export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
   export HDFS_NAMENODE_USER=root
   export HDFS_DATANODE_USER=root
   export HDFS_SECONDARYNAMENODE_USER=root
   export YARN_RESOURCEMANAGER_USER=root
   export YARN_NODEMANAGER_USER=root
   #编辑环境变量后分发到每个节点，并source
   source /etc/profile
   ```

5. 集群规划

   |      | Node01                  | Node02                            | Node03                           |
   | ---- | ----------------------- | --------------------------------- | -------------------------------- |
   | hdfs | ***NameNode***,DataNode | DataNode                          | ***SecondaryNameNode***,DataNode |
   | yarn | NodeManager             | ***ResourceManager***,NodeManager | NodeManager                      |

6. 进入配置路径

   ```shell
   cd /usr/local/hadoop-3.3.1/etc/hadoop
   ```

7. 配置core-site.xml

   ```xml
   <!--指定nameNode的地址-->
   <property>
     <name>fs.defaultFS</name>
     <value>hdfs://node01:8020</value>
   </property>
   <!--指定hadoop数据的存储目录-->
   <property>
     <name>hadoop.tmp.dir</name>
     <value>/usr/local/hadoop-3.3.1/data</value>
   </property>
   <!--配置页面使用的静态用户-->
   <property>
     <name>hadoop.http.staticuser.user</name>
     <value>yknife</value>
   </property>
   
   ```

8. 配置hdfs-site.xml

   ```xml
   <!--nn web端访问地址-->
   <property>
     <name>dfs.namenode.http-address</name>
     <value>node01:9870</value>
   </property>
   <!--2nn web端访问地址-->
   <property>
     <name>dfs.namenode.secondary.http-address</name>
     <value>node03:9868</value>
   </property>
   ```

9. 配置yarn-site.xml

   ```xml
   <!--指定MR走shuffle-->
   <property>
       <name>yarn.nodemanager.aux-services</name>
       <value>mapreduce_shuffle</value>
   </property>
   <!--指定ResourceManager的地址-->
   <property>
       <name>yarn.resourcemanager.hostname</name>
       <value>node02</value>
   </property>
   ```

10. 配置mapred-site.xml

   ```xml
   <!--指定mapReduce程序运行在yarn上-->
   <property>
     <name>mapreduce.framework.name</name>
     <value>yarn</value>
   </property>
   ```

11. 配置workers

    ```shell
    vim workers
    node01
    node02
    node03
    ```

12. 格式化hdfs

    ```shell
    hdfs namenode -format
    ```

13. 在node01启动hdfs

    ```shell
    start-dfs.sh
    ## http://node01:9870/
    ```

14. 启动单个节点

    ```shell
    hdfs --daemon start/stop namenode/datanode/secondarynamenode
    ```

15. 解决root用户启动hdfs报错问题，环境变量增加root用户

    > ERROR: Attempting to operate on hdfs namenode as root
    > ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.

    ```shell
    export HDFS_NAMENODE_USER=root
    export HDFS_DATANODE_USER=root
    export HDFS_SECONDARYNAMENODE_USER=root
    export YARN_RESOURCEMANAGER_USER=root
    export YARN_NODEMANAGER_USER=root
    ```

16. 在node02启动yarn

    ```shell
    start-yarn.sh
    ## http://node02:8088
    ```

17. 启动yarn单个节点

    ```sh
    yarn --daemon start/stop resourcemanager/nodemanager

18. 使用hdfs

    ```shell
    ##创建目录
    hadoop fs -mkdir /input
    ##上传文件
    hadoop fs -put ./LICENSE.txt /input
    ```

19. 执行mapred

    ```shell
    hadoop jar /usr/local/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount /input /output
    ```

20. 执行mapred报错

    > Error: Could not find or load main class org.apache.hadoop.mapreduce.v2.app.MRAppMaster

    ```xml
    ## 编辑mapred-site.xml
    <property>
      <name>yarn.app.mapreduce.am.env</name>
      <value>HADOOP_MAPRED_HOME=/usr/local/hadoop-3.3.1</value>
    </property>
    <property>
      <name>mapreduce.map.env</name>
      <value>HADOOP_MAPRED_HOME=/usr/local/hadoop-3.3.1</value>
    </property>
    <property>
      <name>mapreduce.reduce.env</name>
      <value>HADOOP_MAPRED_HOME=/usr/local/hadoop-3.3.1</value>
    </property>
    ##编辑保存后分发到其他节点，不用重启，再次运行mapred程序即可
    ```

21. hdfs故障恢复

    ```shell
    ## 关闭所有节点
    stop-dfs.sh
    ## 删除每个节点在HADOOP_HOME目录的data和logs目录
    rm -rf data logs
    ## 格式化hdfs
    hdfs namenode -format
    ## 启动hdfs
    start-dfs.sh
    ```

22. 配置mapred执行任务历史服务器

    ```xml
    ##编辑mapred-site.xml
    <!--历史服务器地址-->
    <property>
      <name>mapreduce.jobhistory.address</name>
      <value>node01:10020</value>
    </property>
    <!--历史服务器web端-->
    <property>
      <name>mapreduce.jobhistory.webapp.address</name>
      <value>node01:19888</value>
    </property>
    ```

23. 启动历史服务器

    ```shell
    ## 重启yarn，然后启动历史服务器
    mapred --daemon start historyserver
    ## web地址
    http://node01:19888/
    ```

24. 日志聚集

    ```xml
    ##编辑yarn-site.xml  
    <!--开启日志聚集开关-->
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>
    <!--设置日志聚集访问地址-->
    <property>
        <name>yarn.log.server.url</name>
        <value>http://node01:19888/jobhistory/logs</value>
      </property>
    <!--设置日志保存7天-->
    <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
      </property> 
    ```

    ```shell
    ## 重启历史服务器
    mapred --daemon stop historyserver
    mapred --daemon start historyserver
    ## 重启yarn
    stop-yarn.sh
    start-yarn.sh
    ```

25. 集群时间同步ntpd

    ```shell
    systemctl start ntpd
    systemctl enable ntpd
    systemctl is-enabled ntpd
    vim /etc/ntp.conf
    #修改1（授权192.168.10.0-192.168.10.255网段上的所有机器可以从这台机器上查询和同步时间）
    restrict 192.168.225.0 mask 255.255.255.0 nomodify notrap
    
    #修改2（集群在局域网中，不使用其他互联网上的时间）
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst
    
    #添加3（当该节点丢失网络连接，依然可以采用本地时间作为时间服务器为集群中的其他节点提供时间同步）
    server 127.127.1.0
    fudge 127.127.1.0 stratum 10
    
    vim /etc/sysconfig/ntpd
    #增加内容如下（让硬件时间与系统时间一起同步）
    SYNC_HWCLOCK=yes
    
    #重启node01 ntpd
    systemctl stop ntpd
    systemctl disable ntpd
    
    #关闭node02、node03的ntpd服务（如果已开启的情况）
    systemctl stop ntpd
    systemctl disable ntpd
    
    #在其他机器配置1分钟与时间服务器同步一次
    crontab -e
    */1 * * * * /usr/sbin/ntpdate node01
    
    #可修改时间验证
    date -s "2023-01-01 00:00:00"
    ```
