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

#### 什么是Eureka？

Eureka使用的是C-S架构（客户端-服务端）

两大组件：Eureka Server（提供注册服务）、 Eureka Client（JAVA客户端，负责发送心跳）

Eureka是AP的设计

Zookeeper是CP的设计

#### Eureka的自我保护机制：

​        默认情况下，如果EurekaServer在一定时间内没有收到某个微服务实例的心跳，EurekaServer会将该实例注销（默认90秒），到那时当网络分区发生故障时，微服务与EurekaServer之间无法正常通信，以上行为可能会变的很危险-----因为微服务本身其实是健康的，	此时本不应该注销这个微服务。Eureka通过“自我保护机制”来解决这个问题----当EurekaServer节点在短时间内丢失过多客户端时（可能发生网络分区故障），那么这个节点就会进入自我保护机制。一旦进入该模式，EurekaServer就会保护服务注册表中的信息，不在删除服务注册表中的数据（也就是不会注销任何微服务）。当网络故障恢复后，该Eureka Server节点会自动退出自我保护模式。

​        在自我保护模式中，Eureka Server会保护服务注册表中的信息，不再注销任何服务实例。当它收到的心跳数重新恢复到阈值以上，该Eureka Server节点就会自动退出自我保护模式。它的哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。

#### Eureka Server的使用：

1.启动类上加@EnableEurekaServer

2.向eureka注册的应用名称：spring.application.name

3.不向eureka注册自己：eureka.client.register-with-eureka:false

4.表示自己就是注册中心，职责是维护服务实例，不需要去检索服务：eureka.client.fetch-registry:false

​	eureka的自我保护机制：eureka会和已经注册在自己这里的服务保持心跳连接，如果心跳连接出现异常（掉线，服务关闭）等情况，eureka会计算一个比率，然后提示在注册中心中。但是，eureka不会将这个服务摘除，而是继续去尝试调用该服务。（默认90秒没有收到心跳连接，就摘除该服务）

​	禁用eureka的自我保护机制（注意：只能在开发环境使用此配置）：eureka.server.enable-self-preservation:false

#### Eureka Client的使用：

1.启动类添加@EnableDiscoveryClient

2.配置注册中心地址:eureka.client.server-url.defaultZone

3.为了使服务更好区分，可以给当前服务起别名：eureka.instance.instance-id

#### Eureka的高可用（集群）：

注意：在本机测试eureka集群，需要提前配置好C:\Windows\System32\drivers\etc\hosts

server端向其他eureka注册自己，以实现高可用：eureka.client.server-url.defaultZone：其他eureka的地址

#### Eureka的服务发现：

系统中的微服务可以通过Eureka的服务发现去获得在Eureka中注册的服务的信息，这是一个对外暴露的接口。

##### 使用方法（provider中）

1. 注入DiscoveryClient 对象（spring包下的），在controller方法中获取

```java
@Autowired
private DiscoveryClient discoveryClient;    

@ResponseBody
@GetMapping("/provider/discovery")
public Object discovery(){
        List<String> list = discoveryClient.getServices();
        System.out.println(list);
        List<ServiceInstance> insList = discoveryClient.getInstances("MICROSERVICECLOUD-DEPT");
        for (ServiceInstance si:insList) {
            System.out.println(si.getHost() +"," + si.getServiceId() +"," +si.getPort() +"," +si.getUri() +"," +si.getMetadata());
        }
        return this.discoveryClient;
    }
```

1. 在主启动类中加入@EnableDiscoveryClient注解

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class Provider8001_APP {
    public static void main(String[] args) {
        SpringApplication.run(Provider8001_APP.class,args);
    }
}
```

##### 使用方法（consumer中）

在controller方法中使用restTemplate对象调用provider中暴露的URL 并获得返回对象即可

```java
@GetMapping("/discovery")
public Object discovery() {
        return restTemplate.getForObject(URL_PREFIX+"/provider/discovery",Object.class);
    }
```

#### 实现服务发现的两种方式：

客户端发现：Eureka

服务端发现：Nginx、Zookeeper、Kubernetes

#### 为什么Spring Cloud选择了客户端发现的方式？

微服务的特点：异构（不同语言、不同类型数据库）

#### Eureka和Zookeeper：

| Zookeeper                                                    | Eureka                                                       |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| 当网络出现故障时，剩余zk集群会发起投票选举新的leader，但是此过程会持续30~120s，此过程对于高并发来说十分漫长，会导致整个注册服务的瘫痪，这是不可容忍的 | 在15分钟内85%的节点都没有心跳，则注册中心 会认为客户端与之出现了网络故障，则会进入自动保护模式。1.Eureka不会移除没有收到心跳的服务；2.新的服务仍能在服务端注册，但是暂时不会被同步到其他节点上直到网络稳定 |

zookeeper：cp

Eureka：ap

Eureka可以很好的应对网络故障导致部分节点失去连接的情况，而不会像zookeeper那样导致整个注册服务系统的瘫痪。