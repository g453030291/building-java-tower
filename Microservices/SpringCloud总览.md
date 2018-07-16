Eureka：服务注册与发现

rest+Ribbon：服务消费者

Feign：服务消费者

Hystrix：断路器

Hystrix Dashboard：断路器监控

Zuul：路由网关

Spring Cloud Config：分布式配置中心

Spring Cloud Bus：消息总线

Spring Cloud Sleuth：服务链路追踪

项目构建：

maven，父工程构建为pom，来管理子工程jar包版本，pom依赖。

构建子工程，把通用api都放进去。通用entity类。子工程需要，maven clean、maven install。



