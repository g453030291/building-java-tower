#### Feign是什么？

Feign是一个声明式的Web服务客户端，使得编写Web服务客户端变的容易，只需要创建一个接口，在上面添加注解即可。

#### Feign能干嘛？

Feign旨在编写Java Http客户端变得更容易。

前面在使用Ribbon+RestTemplate时，利用RestTemplate对http请求进行封装处理。但在实际开发中，对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装（以前是在Dao接口上标注@Mapper注解，现在是一个微服务接口标注一个Feign注解）。即可完成对服务提供方的接口绑定。简化了使用Ribbon时，自动封装服务调用客户端的开发量。

#### 理解：

Feign其实就是面向接口编程的SpringCloud版。之前使用的RestTemplate+Ribbon微服务调用是一种调用服务的方式，使用Feign（接口+注解）的方式调用，也是一种调用服务的方式。而且是开源社区大家都能接受的一种方式。

#### Feign

#### 特点：

声明式的REST客户端（feign的使用全都在客户端）

采用了基于接口加注解的方式

使用体验上，就像rpc一样，远程调用方法。但是其实feign内部还是使用的http，负载均衡还是使用的ribbon

#### 使用：

启动主类上添加@EnableFeignClients（如果调用不到，需要在这个注解后加上basePackages=“扫描的包路径”）

接口上添加注解@FeignClient（name="product"）

在使用的地方，直接@autowire就可以使用。

#### Feign集成了Ribbon：

利用Ribbon维护了MicroServiceCloud-Dept的服务列表信息，并且通过轮询实现了客户端的负载均衡，而与Ribbon不同的是，通过Feign只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用。

#### 调用SpringCloud微服务的两种方式：

调用微服务，有两种方式：

1.RestTemplate+Ribbon（面向微服务编程）

2.Interface+Feign（面向接口编程）