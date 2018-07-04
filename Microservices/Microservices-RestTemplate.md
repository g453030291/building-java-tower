类似于httpclient，使用RestTemplate有三种方式：

第一种：(直接使用restTemplate，url写死)

```java
RestTemplate restTemplate = new RestTemplate();

String response = restTemplate.getForObject("http://localhost:8080/msg",String.class);

```



第二种：(利用loadBanlanceClient通过应用名称获取url，再使用restTemplate)

```java
@Autowired

private LoadBanlancerClient loadBalancerClient;

RestTemplate restTemplate = new RestTemplate();
ServiceInstance serviceInstance = loadBalancerClient.choose("PRODUCT");
String url = String format("http://%s:%s",serviceInstance.getHost(),serviceInstance.getPort());
String response = restTemplate.getForObject(url,String.class);
```

第三种：(利用@LoadBanlanced，可在restTemplate里使用应用名称)

```java
@Component
public class RestTemplateConfig{//首先将restTemplate对象注入到spring容器
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}

controller：
String response = restTemplate.getForObject("http://PRODUCT/msg",String.class);

```

