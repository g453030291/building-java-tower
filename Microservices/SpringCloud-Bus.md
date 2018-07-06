### SpringCloudBus

原理：

通过消息队列，

步骤：

configService中配置：management.endpoints.web.expose:"*"暴露所有接口

使用远程服务配置的类上加上@RefreshScope

访问接口，项目地址/actuator/bus-refresh

使用git的WebHooks功能来实现，修改git代码，自动访问接口地址，自动刷新配置的效果