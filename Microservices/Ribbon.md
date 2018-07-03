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