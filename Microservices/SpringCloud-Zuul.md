#### 为什么需要服务网关，zuul

避免请求，发送到每一个微服务

#### 特点：

zuul=路由+过滤器

zuul的核心是一系列的过滤器

zuul的四种过滤器API：

前置（pre）

后置（post）

路由（Route）

错误（Error）

#### 实现步骤：

启动类添加注解@EnableZuulProxy

eureka中的注册的服务，都可以使用zuul来实现路由的功能，zuul的服务地址/服务名/服务的访问url

可以实现自定义服务地址，在访问的微服务yml中配置zuul.routs.myproduct.path:/自定义的地址/**

zuul.routs.myproduct.serviceID:路由到的服务名

可以再zuul中，查看所有配置的路由映射地址：management.security.enabled:false

排除某些路由：zuul.routs. ignored-patterns:- 路由地址

#### 注意：

zuul默认禁止cookie的传递。从后台request中是获取不到cookie的。

如果需要获取cookie需要设置：zuul.routs. sensitiveHeaders：（为空就行）

#### 动态路由：

使用bus+config来实现配置的自动更新。然后使用下面的配置来将动态修改的yml配置，更新到代码中

启动类中配置:

````java
@configurationproperties("zuul")
@refreshscope
public ZuulProperties zuulProperties(){
    return new ZuulProperties();
}
````

#### 应用场景：

前置:

限流、鉴权、参数校验调整

后置：

统计

#### 实现zuul的pre鉴权：

新建一个tokenFilter类，继承zuulFilter

重写里面的filterType、filterOrder、shouldFilder、fun方法

```java
@Component
public class TokenFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return PRE_TYPE;//filter类型
    }

    @Override
    public int filterOrder() {
        return PRE_DECORATION_FILTER_ORDER - 1;//filter执行顺序。数字越小代表执行越靠前
    }

    @Override
    public boolean shouldFilter() {
        return true;//是否启用
    }

    @Override
    public Object run() {
        RequestContext requestContext = RequestContext.getCurrentContext();
        HttpServletRequest request = requestContext.getRequest();//获取到request
		//下面代码做业务处理

//            requestContext.setSendZuulResponse(false);
//            requestContext.setResponseStatusCode(HttpStatus.UNAUTHORIZED.value());
        return null;
    }
}

```

#### zuul限流：

使用令牌桶算法实现。

使用guava的RateLimiter

#### zuul权限校验：

zuul.sensitice-headers：全部服务忽略敏感头，全部服务都可以传递cookie

#### zuul总结：

在微服务框架中，用户访问各个微服务都需要确定用户不同的权限，以方便用户访问不同的微服务。在springcloud中，使用zuul的前置filter来实现用户的权限控制。

实现分布式下用户状态的获取，常用有两种解决方案：

一、使用分布式session，存储在redis中

二、spirngsecurity+oAuth2

#### zuul跨域：

两种解决方案：

1.在服务调用的类或者方法上加上@CrossOrigin（allowCredentials="true"表示支持cookie跨域）注解，声明自己支持跨域访问

2.zuul增加crosFilter过滤器

```java
@configuration
public class CorsConfig{
    @Bean
    public CorsFilter corsFilter(){
        final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        finla CorsConfiguration config = new CorsConfiguration();
        
        config.setAllowCredentials(ture);
        config.setAllowedOrigins(Arrays.asList("*"));
        config.setAllowedHeaders(Arrays.asList("*"));
        config.setAllowedMethods(Arrays.asList("*"));
        config.setMaxAge(300l);
        
        source.registerCorsCofiguration("/**",config);
        return new CorsFilter(source);
    }
    
}
```

