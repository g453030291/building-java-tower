#### 链路监控Spring Cloud Sleuth：

tranceId：链路的唯一标识

spanId：基本的工作单元（比如发送一次http请求）

true/false：是否向其他外部发送链路信息来实现链路监控

#### 图形化展示界面zipkin：

#### 注意：

引入maven依赖的时候，可以引入，spring-cloud-starter-zipkin

这个依赖已经包含了Spring-Cloud-Sleuth+Spring-Cloud-Sleuth-zipkin

配置：

在yml中配置spring.zipkin.base-url：http://localhost:8080/

spring.sleuth.samler.percentage：抽样的百分比。默认是0.1（表示10%）

关键概念：

traceID：全局的跟踪id，在入口生成traceID

spanID：下一层的请求跟踪id

parentID：上一层的请求跟踪id

#### 分布式追踪系统：

