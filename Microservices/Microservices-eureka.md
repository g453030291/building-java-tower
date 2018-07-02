#### 什么是微服务？

和RestFul类似，是一种架构风格，由马丁福勒提出。

1.一系列微小的服务共同组成；

2.跑在自己的进程中；

3.每个服务为独立的业务开发；

4.独立部署；

5.分布式管理；

#### 什么是分布式？

旨在支持应用程序和服务的开发，可以利用物理架构，由多个自治的处理元素，不共享主内存，但通过网络发送消息合作。

Spring Cloud Eureka:

由两个组件构成：

​	-Eureka Server 注册中心

​	-Eureka Client  服务注册

#### Eureka Server的使用：

1.启动类上加@EnableEurekaServer

2.向eureka注册的应用名称：spring.application.name

3.不向eureka注册自己：eureka.client.register-with-eureka:false

​	eureka的自我保护机制：eureka会和已经注册在自己这里的服务保持心跳连接，如果心跳连接出现异常（掉线，服务关闭）等情况，eureka会计算一个比率，然后提示在注册中心中。但是，eureka不会将这个服务摘除，而是继续去尝试调用该服务。

​	禁用eureka的自我保护机制（注意：只能在开发环境使用此配置）：eureka.server.enable-self-preservation:false

#### Eureka Client的使用：

1.启动类添加@EnableDiscoveryClient

2.配置注册中心地址:eureka.client.server-url.defaultZone

#### Eureka的高可用（集群）：

server端向其他eureka注册自己，以实现高可用：eureka.client.server-url.defaultZone：其他eureka的地址

#### 实现服务发现的两种方式：

客户端发现：Eureka

服务端发现：Nginx、Zookeeper、Kubernetes

#### 为什么Spring Cloud选择了客户端发现的方式？

微服务的特点：异构（不同语言、不同类型数据库）

