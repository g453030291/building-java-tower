SpirngCloud实现的是软负载，通过Ribbon组件；

用到Ribbon实现软负载的模块还有RestTemplate、Feign、Zuul

#### 核心：

服务发现

服务选择规则

服务监听

#### 主要组件：

ServerList、IRule、ServerListFilter

#### 运行流程：

​	首先通过ServerList获取所有可用的服务列表，然后通过ServerListFilter过滤掉一部分地址，最后剩下的地址中，使用IRule选择一个实例，作为最终目标结果。

#### 自定义负载均衡规则：

yml中配置：PRODUCT.ribbon.NFLoadBlancerRuleClassName:全类名

product是对应的服务名





#### Feign

特点：

声明式的REST客户端（feign的使用全都在客户端）

采用了基于接口加注解的方式

使用体验上，就像rpc一样，远程调用方法。但是其实feign内部还是使用的http，负载均衡还是使用的ribbon

使用：

启动主类上添加@EnableFeignClients（如果调用不到，需要在这个注解后加上basePackages=“扫描的包路径”）

接口上添加注解@FeignClient（name="product"）

在使用的地方，直接@autowire就可以使用。

