### ConfigServer

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

