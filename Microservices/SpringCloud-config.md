### ConfigServer（实现编码-配置解耦）

#### 是什么？

SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置，分为服务端和客户端

#### 能解决什么问题？

①：集中管理配置文件

②：不同环境不同配置，动态的配置更新，分环境部署比如dev/test/prod

③：运行期间动态调整配置，不再需要在每个服务的机器上编写配置文件，服务会向配置中心统一拉取配置自己的配置信息

④：当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置

⑤：将配置信息以REST接口的形式暴露

#### 为什么需要统一配置中心

不方便维护

配置内容安全与权限

更新配置项目需要重启

#### 配置步骤

@EnableDiscoveryClient//表示eureka的client端，要注册到eureka的server中

@EnableConfigServer//表示config

spring.cloud.config.server.git.uri：git仓库地址

spring.cloud.config.server.git.username

spring.cloud.config.server.git.password

可以在yml中添加env配置不同环境

basedir可以配置从远端git拉取到本地git的文件存放地址

查看：

可以通过访问localhost:8080/order-a.yml(或者properties/json等格式都可以)

### ConfigClient

#### 配置步骤

spirng.cloud.config.discovery.enabled:true

spirng.cloud.config.discovery.service-id：eureka中config-service的name

spirng.cloud.config.profile	:dev（配置选取哪个环境）

**将application.yml改为bootstrap.yml改了以后才能让springboot首先下载配置文件，然后在启动**

