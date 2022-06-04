* 修改8001和80的yaml文件，将它们的配置文件的eureka.client.service-url.defaultZone进行修改

  ```yaml
  eureka:
    client:
      #表示是否将自己注册进Eurekaserver默认为true。
      register-with-eureka: true
      #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
      fetchRegistry: true
      service-url:
        defaultZone: http://eureka7001.com:7001/eureka, http://eureka7002.com:7002/eureka
  ```

  