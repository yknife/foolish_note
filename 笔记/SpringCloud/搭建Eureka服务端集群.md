1. 编辑hosts文件

   ```sh
   127.0.0.1 eureka7001.com
   127.0.0.1 eureka7002.com
   ```

2. 修改cloud-eureka-server7001配置文件

   ```yaml
   server:
     port: 7001
   
   eureka:
     instance:
       hostname: eureka7001.com #eureka服务端的实例名称
     client:
       register-with-eureka: false     #false表示不向注册中心注册自己。
       fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
       service-url:
       #集群指向其它eureka
         defaultZone: http://eureka7002.com:7002/eureka/
       #单机就是7001自己
         #defaultZone: http://eureka7001.com:7001/eureka/
   ```

3. 修改cloud-eureka-server7002配置文件

   ```yaml
   server:
     port: 7002
   
   eureka:
     instance:
       hostname: eureka7002.com #eureka服务端的实例名称
     client:
       register-with-eureka: false     #false表示不向注册中心注册自己。
       fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
       service-url:
       #集群指向其它eureka
         defaultZone: http://eureka7001.com:7001/eureka/
       #单机就是7002自己
         #defaultZone: http://eureka7002.com:7002/eureka/
   ```

   