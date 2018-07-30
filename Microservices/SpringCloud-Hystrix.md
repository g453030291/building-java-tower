#### Hystrix是什么？

Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统中，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

“断路器”本身是一种开关装置，当某个服务单元发生故障后，通过断路器的故障监控	（类似熔断的保险丝），向调用方返回一个符合预期的、可处理的备选响应（FullBack），而不是长时间的等待或者抛出方法无法调用的异常，这样就保证了服务调用线程不会被长时间、不必要的占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

#### Hystrix：

作用，防止雪崩效应。

#### 功能：

##### 服务熔断：

###### 概念：

向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常。发生异常时，会进行服务的降级，进而熔断该节点微服务调用，快速返回错误信息。

理解：

类似于Spring的异常通知，Hystrix监控服务的异常，如果你在方法中抛出一个RuntimeException，Hystrix会捕获到异常，并且调用fallback方法来返回一个可预期的错误相应。

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

##### 服务降级：

理解：上面的服务熔断的处理方式，由于需要在每一个controller类中的方法上添加@hystrixCommand（fallback=“”）注解，然后需要写一个异常处理类，供fallback调用。这种处理方式，耦合性太高，且代码重复过多，所以

1.可以使用在api工程中，新建一个xxxxxfallback类，implement FallBackFactory<orderService>，然后在creat方法中，new 出接口类，然后对每个方法再添加异常处理。（这个类需要添加@component）

2.然后修改service接口上的注解@FeignClient（value=“maosy-order”，fallbackfactory=xxxxxfallback.class）。

3.api中的yml添加feign.hystrix.enabled:true



优先核心服务，非核心服务不可用或弱可用

通过HystrixCommand注解指定

fallbackMethod（回退函数）中具体实现降级逻辑

类名上可以加，@DefaultProperites（defaultFallback=“defaultFallback”）

设置统一的fallback提示

设置超时时间@HystrixCommand（commandProperties={@hYSTRIX

pProperty(name="",value="")}）

#### 服务熔断、服务降级理解：

服务熔断：一般是某个服务故障，类似现实生活中的保险丝，当某个异常条件被触发，直接熔断整个服务，而不是一直等到此服务超时。

服务降级：所谓降级，一般从整体负荷考虑，当某个服务熔断，服务器将不再被调用。此时客户端可以自己准备一个本地的fallback回调，返回一个缺省值，这样做虽然服务水平下降，但好歹可用，比直接挂掉强。

熔断、降级其实是一体的，服务端触发某个exception或者超时等情况，hystrix会监控到，然后就会给该服务降级，同时，调用客户端的fallback方法，来向用户返回一个异常。

##### 依赖隔离：

类似于docker的舱壁模式，docker通过舱壁模式，实现进程隔离，使得进程之间互不影响。而Hystrix使用舱壁模式实现线程池的隔离，会为每个@HystrixCommand创建一个独立的线程池，这样某个@HystrixCommand包装下的服务，出现了宕机，超时等问题。也只会影响这一个服务，并不会拖慢其他服务。

@HystrixCommand自动使用Hystrix实现依赖隔离。



##### 监控（Hystrix Dashboard）

启动类上加注解@EnabledHystrixDashboard

yml中加上：management.contantpath： /

zuul结合hystrix配置超时：

yml中配置：

hystrix.command.default.execution.isolation.thread.timeoutinmilliseconds:5000