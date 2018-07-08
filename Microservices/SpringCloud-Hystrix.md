#### Hystrix：

作用，防止雪崩效应。

#### 功能：

##### 服务降级：

优先核心服务，非核心服务不可用或弱可用

通过HystrixCommand注解指定

fallbackMethod（回退函数）中具体实现降级逻辑

类名上可以加，@DefaultProperites（defaultFallback=“defaultFallback”）

设置统一的fallback提示

设置超时时间@HystrixCommand（commandProperties={@hYSTRIX

pProperty(name="",value="")}）

##### 依赖隔离：

类似于docker的舱壁模式，docker通过舱壁模式，实现进程隔离，使得进程之间互不影响。而Hystrix使用舱壁模式实现线程池的隔离，会为每个@HystrixCommand创建一个独立的线程池，这样某个@HystrixCommand包装下的服务，出现了宕机，超时等问题。也只会影响这一个服务，并不会拖慢其他服务。

@HystrixCommand自动使用Hystrix实现依赖隔离。

##### 服务熔断：

@HystrixCommand（commandProperties={@hYSTRIX

pProperty(name="",value="")}）

name有三个需要配置：

circuitBreaker.requestVolumeThreshold,value=10

circuitBreaker.sleepWindowInMilliseconds,value=10000

circuitBreaker.errorThresholdPercentage,calue=60

可以将这些配置都配置在yml配置文件中（依然要在需要服务降级的类或者方法上加上@HystrixCommand）

###### 在feign中使用hystrix

1.yml中配置feigh.hystrix.enabled：true

2.FeignClient（fallback=“”）

这里有坑，要是用实现类使用fallback的方法，必须加上@component注解，加上注解后如果你是在子项目中假的注解，它会以jar包的形式来打包到父项目，但是，父项目中扫描不到这个类，你必须在父项目的启动类中设置@ComponentScan（basepackages=""）来设置扫描路径

##### 监控（Hystrix Dashboard）

启动类上加注解@EnabledHystrixDashboard

yml中加上：management.contantpath： /

zuul结合hystrix配置超时：

yml中配置：

hystrix.command.default.execution.isolation.thread.timeoutinmilliseconds:5000