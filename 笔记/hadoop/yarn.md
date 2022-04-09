1. 容量调度器基本配置，编辑yarn-site.xml,追加

   ```xml
   <!-- 选择调度器，默认容量 -->
   <property>
   	<description>The class to use as the resource scheduler.</description>
   	<name>yarn.resourcemanager.scheduler.class</name>
   	<value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
   </property>
   
   <!-- ResourceManager处理调度器请求的线程数量,默认50；如果提交的任务数大于50，可以增加该值，但是不能超过3台 * 4线程 = 12线程（去除其他应用程序实际不能超过8） -->
   <property>
   	<description>Number of threads to handle scheduler interface.</description>
   	<name>yarn.resourcemanager.scheduler.client.thread-count</name>
   	<value>8</value>
   </property>
   
   <!-- 是否让yarn自动检测硬件进行配置，默认是false，如果该节点有很多其他应用程序，建议手动配置。如果该节点没有其他应用程序，可以采用自动 -->
   <property>
   	<description>Enable auto-detection of node capabilities such as
   	memory and CPU.
   	</description>
   	<name>yarn.nodemanager.resource.detect-hardware-capabilities</name>
   	<value>false</value>
   </property>
   
   <!-- 是否将虚拟核数当作CPU核数，默认是false，采用物理CPU核数 -->
   <property>
   	<description>Flag to determine if logical processors(such as
   	hyperthreads) should be counted as cores. Only applicable on Linux
   	when yarn.nodemanager.resource.cpu-vcores is set to -1 and
   	yarn.nodemanager.resource.detect-hardware-capabilities is true.
   	</description>
   	<name>yarn.nodemanager.resource.count-logical-processors-as-cores</name>
   	<value>false</value>
   </property>
   
   <!-- 虚拟核数和物理核数乘数，默认是1.0 -->
   <property>
   	<description>Multiplier to determine how to convert phyiscal cores to
   	vcores. This value is used if yarn.nodemanager.resource.cpu-vcores
   	is set to -1(which implies auto-calculate vcores) and
   	yarn.nodemanager.resource.detect-hardware-capabilities is set to true. The	number of vcores will be calculated as	number of CPUs * multiplier.
   	</description>
   	<name>yarn.nodemanager.resource.pcores-vcores-multiplier</name>
   	<value>1.0</value>
   </property>
   
   <!-- NodeManager使用内存数，默认8G，修改为4G内存 -->
   <property>
   	<description>Amount of physical memory, in MB, that can be allocated 
   	for containers. If set to -1 and
   	yarn.nodemanager.resource.detect-hardware-capabilities is true, it is
   	automatically calculated(in case of Windows and Linux).
   	In other cases, the default is 8192MB.
   	</description>
   	<name>yarn.nodemanager.resource.memory-mb</name>
   	<value>1024</value>
   </property>
   
   <!-- nodemanager的CPU核数，不按照硬件环境自动设定时默认是8个，修改为4个 -->
   <property>
   	<description>Number of vcores that can be allocated
   	for containers. This is used by the RM scheduler when allocating
   	resources for containers. This is not used to limit the number of
   	CPUs used by YARN containers. If it is set to -1 and
   	yarn.nodemanager.resource.detect-hardware-capabilities is true, it is
   	automatically determined from the hardware in case of Windows and Linux.
   	In other cases, number of vcores is 8 by default.</description>
   	<name>yarn.nodemanager.resource.cpu-vcores</name>
   	<value>1</value>
   </property>
   
   <!-- 容器最小内存，默认1G -->
   <property>
   	<description>The minimum allocation for every container request at the RM	in MBs. Memory requests lower than this will be set to the value of this	property. Additionally, a node manager that is configured to have less memory	than this value will be shut down by the resource manager.
   	</description>
   	<name>yarn.scheduler.minimum-allocation-mb</name>
   	<value>256</value>
   </property>
   
   <!-- 容器最大内存，默认8G，修改为2G -->
   <property>
   	<description>The maximum allocation for every container request at the RM	in MBs. Memory requests higher than this will throw an	InvalidResourceRequestException.
   	</description>
   	<name>yarn.scheduler.maximum-allocation-mb</name>
   	<value>1024</value>
   </property>
   
   <!-- 容器最小CPU核数，默认1个 -->
   <property>
   	<description>The minimum allocation for every container request at the RM	in terms of virtual CPU cores. Requests lower than this will be set to the	value of this property. Additionally, a node manager that is configured to	have fewer virtual cores than this value will be shut down by the resource	manager.
   	</description>
   	<name>yarn.scheduler.minimum-allocation-vcores</name>
   	<value>1</value>
   </property>
   
   <!-- 容器最大CPU核数，默认4个，修改为2个 -->
   <property>
   	<description>The maximum allocation for every container request at the RM	in terms of virtual CPU cores. Requests higher than this will throw an
   	InvalidResourceRequestException.</description>
   	<name>yarn.scheduler.maximum-allocation-vcores</name>
   	<value>1</value>
   </property>
   
   <!-- 虚拟内存检查，默认打开，修改为关闭 -->
   <property>
   	<description>Whether virtual memory limits will be enforced for
   	containers.</description>
   	<name>yarn.nodemanager.vmem-check-enabled</name>
   	<value>false</value>
   </property>
   
   <!-- 虚拟内存和物理内存设置比例,默认2.1 -->
   <property>
   	<description>Ratio between virtual memory to physical memory when	setting memory limits for containers. Container allocations are	expressed in terms of physical memory, and virtual memory usage	is allowed to exceed this allocation by this ratio.
   	</description>
   	<name>yarn.nodemanager.vmem-pmem-ratio</name>
   	<value>2.1</value>
   </property>
   ```

2. 增加队列，编辑capacity-scheduler.xml，可配置单个队列占root容量的百分比，所有的root.default配置，新的队列也需要有

   ```xml
   <property>
       <name>yarn.scheduler.capacity.root.queues</name>
       <value>default,yknife</value>
       <description>
         The queues at the this level (root is the root queue).
       </description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.capacity</name>
       <value>40</value>
       <description>Default queue target capacity.</description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.capacity</name>
       <value>60</value>
       <description>Default queue target capacity.</description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.user-limit-factor</name>
       <value>1</value>
       <description>
         Default queue user limit a percentage from 0.0 to 1.0.
       </description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.user-limit-factor</name>
       <value>1</value>
       <description>
         Default queue user limit a percentage from 0.0 to 1.0.
       </description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.maximum-capacity</name>
       <value>60</value>
       <description>
         The maximum capacity of the default queue. 
       </description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.maximum-capacity</name>
       <value>80</value>
       <description>
         The maximum capacity of the default queue. 
       </description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.state</name>
       <value>RUNNING</value>
       <description>
         The state of the default queue. State can be one of RUNNING or STOPPED.
       </description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.state</name>
       <value>RUNNING</value>
       <description>
         The state of the default queue. State can be one of RUNNING or STOPPED.
       </description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.acl_submit_applications</name>
       <value>*</value>
       <description>
         The ACL of who can submit jobs to the default queue.
       </description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.acl_submit_applications</name>
       <value>*</value>
       <description>
         The ACL of who can submit jobs to the default queue.
       </description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.acl_administer_queue</name>
       <value>*</value>
       <description>
         The ACL of who can administer jobs on the default queue.
       </description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.acl_administer_queue</name>
       <value>*</value>
       <description>
         The ACL of who can administer jobs on the default queue.
       </description>
     </property>
   
     <property>
       <name>yarn.scheduler.capacity.root.default.acl_application_max_priority</name>
       <value>*</value>
       <description>
         The ACL of who can submit applications with configured priority.
         For e.g, [user={name} group={name} max_priority={priority} default_priority={priority}]
       </description>
     </property>
     <property>
       <name>yarn.scheduler.capacity.root.yknife.acl_application_max_priority</name>
       <value>*</value>
       <description>
         The ACL of who can submit applications with configured priority.
         For e.g, [user={name} group={name} max_priority={priority} default_priority={priority}]
       </description>
     </property>
   
      <property>
        <name>yarn.scheduler.capacity.root.default.maximum-application-lifetime
        </name>
        <value>-1</value>
        <description>
           Maximum lifetime of an application which is submitted to a queue
           in seconds. Any value less than or equal to zero will be considered as
           disabled.
           This will be a hard time limit for all applications in this
           queue. If positive value is configured then any application submitted
           to this queue will be killed after exceeds the configured lifetime.
           User can also specify lifetime per application basis in
           application submission context. But user lifetime will be
           overridden if it exceeds queue maximum lifetime. It is point-in-time
           configuration.
           Note : Configuring too low value will result in killing application
           sooner. This feature is applicable only for leaf queue.
        </description>
      </property>
      <property>
        <name>yarn.scheduler.capacity.root.yknife.maximum-application-lifetime
        </name>
        <value>-1</value>
        <description>
           Maximum lifetime of an application which is submitted to a queue
           in seconds. Any value less than or equal to zero will be considered as
           disabled.
           This will be a hard time limit for all applications in this
           queue. If positive value is configured then any application submitted
           to this queue will be killed after exceeds the configured lifetime.
           User can also specify lifetime per application basis in
           application submission context. But user lifetime will be
           overridden if it exceeds queue maximum lifetime. It is point-in-time
           configuration.
           Note : Configuring too low value will result in killing application
           sooner. This feature is applicable only for leaf queue.
        </description>
      </property>
   
      <property>
        <name>yarn.scheduler.capacity.root.default.default-application-lifetime
        </name>
        <value>-1</value>
        <description>
           Default lifetime of an application which is submitted to a queue
           in seconds. Any value less than or equal to zero will be considered as
           disabled.
           If the user has not submitted application with lifetime value then this
           value will be taken. It is point-in-time configuration.
           Note : Default lifetime can't exceed maximum lifetime. This feature is
           applicable only for leaf queue.
        </description>
      </property>
      <property>
        <name>yarn.scheduler.capacity.root.yknife.default-application-lifetime
        </name>
        <value>-1</value>
        <description>
           Default lifetime of an application which is submitted to a queue
           in seconds. Any value less than or equal to zero will be considered as
           disabled.
           If the user has not submitted application with lifetime value then this
           value will be taken. It is point-in-time configuration.
           Note : Default lifetime can't exceed maximum lifetime. This feature is
           applicable only for leaf queue.
        </description>
      </property>
   ```

3. 重启yarn或者刷新队列配置，注：修改capacity-scheduler.xml可不重启yarn，但是如果修改yarn-site.xml则必须重启yarn

   ```shell
   yarn rmadmin -refreshQueues
   ```

4. 可在http://node02:8088/查看更新队列配置后，配置的变化

5. 在指定的队列上执行任务

   ```shell
   hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount -D mapreduce.job.queuename=yknife /input /output1
   ```

6. 设置任务优先级，编辑yarn-site.xml

   ```xml
   <property>
       <name>yarn.cluster.max-application-priority</name>
       <value>5</value>
   </property>
   ```

7. 重启yarn，在node02重启yarn

8. 多次运行任务，观察设置优先级的任务是否优先执行

   ```shell
    hadoop jar /usr/local/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar pi 5 2000000
    hadoop jar /usr/local/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar pi -D mapreduce.job.priority=5 5 2000000
    //也可在运行过程中修改任务的优先级
    yarn application -appID application_1643899208197_0003 -updatePriority 5
   ```

9. 配置使用公平调度器编辑yarn-site.xml和fair-scheduler.xml

   ```xml
   <!--yarn-site.xml-->
   <property>
       <name>yarn.resourcemanager.scheduler.class</name>
       <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler</value>
       <description>配置使用公平调度器</description>
   </property>
   
   <property>
       <name>yarn.scheduler.fair.allocation.file</name>
       <value>/opt/module/hadoop-3.1.3/etc/hadoop/fair-scheduler.xml</value>
       <description>指明公平调度器队列分配配置文件</description>
   </property>
   
   <property>
       <name>yarn.scheduler.fair.preemption</name>
       <value>false</value>
       <description>禁止队列间资源抢占</description>
   </property>
   ```

   ```xml
   <!--fair-scheduler.xml-->
   <?xml version="1.0"?>
   
   <allocations>
   
     <!-- 单个队列中Application Master占用资源的最大比例,取值0-1 ，企业一般配置0.1 -->
   
     <queueMaxAMShareDefault>0.5</queueMaxAMShareDefault>
   
     <!-- 单个队列最大资源的默认值 test atguigu default -->
   
     <queueMaxResourcesDefault>1536mb,1vcores</queueMaxResourcesDefault>
   
   
   
     <!-- 增加一个队列test -->
   
     <queue name="test">
   
       <!-- 队列最小资源 -->
   
       <minResources>1024mb,1vcores</minResources>
   
       <!-- 队列最大资源 -->
   
       <maxResources>2048mb,1vcores</maxResources>
   
       <!-- 队列中最多同时运行的应用数，默认50，根据线程数配置 -->
   
       <maxRunningApps>4</maxRunningApps>
   
       <!-- 队列中Application Master占用资源的最大比例 -->
   
       <maxAMShare>0.75</maxAMShare>
   
       <!-- 该队列资源权重,默认值为1.0 -->
   
       <weight>1.0</weight>
   
       <!-- 队列内部的资源分配策略 -->
   
       <schedulingPolicy>fair</schedulingPolicy>
   
     </queue>
   
     <!-- 增加一个队列atguigu -->
   
     <queue name="atguigu">
   
       <!-- 队列最小资源 -->
   
       <minResources>1024mb,1vcores</minResources>
   
       <!-- 队列最大资源 -->
   
       <maxResources>2048mb,1vcores</maxResources>
   
       <!-- 队列中最多同时运行的应用数，默认50，根据线程数配置 -->
   
       <maxRunningApps>4</maxRunningApps>
   
       <!-- 队列中Application Master占用资源的最大比例 -->
   
       <maxAMShare>0.75</maxAMShare>
   
       <!-- 该队列资源权重,默认值为1.0 -->
   
       <weight>1.0</weight>
   
       <!-- 队列内部的资源分配策略 -->
   
       <schedulingPolicy>fair</schedulingPolicy>
   
     </queue>
   
   
   
     <!-- 任务队列分配策略,可配置多层规则,从第一个规则开始匹配,直到匹配成功 -->
   
     <queuePlacementPolicy>
   
       <!-- 提交任务时指定队列,如未指定提交队列,则继续匹配下一个规则; false表示：如果指定队列不存在,不允许自动创建-->
   
       <rule name="specified" create="false"/>
   
       <!-- 提交到root.group.username队列,若root.group不存在,不允许自动创建；若root.group.user不存在,允许自动创建 -->
   
       <rule name="nestedUserQueue" create="true">
   
           <rule name="primaryGroup" create="false"/>
   
       </rule>
   
       <!-- 最后一个规则必须为reject或者default。Reject表示拒绝创建提交失败，default表示把任务提交到default队列 -->
   
       <rule name="reject" />
   
     </queuePlacementPolicy>
   
   </allocations>
   ```

10. 重启yarn

11. 执行命令，指定任务在不同的队列执行

    ```shell
    hadoop jar /usr/local/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar pi -D mapreduce.job.queuename=root.test 1 1
    ```

12. 删除卡死任务

    ```shell
    yarn application -kill application_1612577921195_0001
    ```

    