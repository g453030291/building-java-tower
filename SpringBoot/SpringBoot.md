#### 一、启动类上的注解

主启动类上的注解@SpringBootApplication

````java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration//标注这是一个SpringBoot的配置类，和Spirng的@Configuration是一个含义，都是标注配置类
@EnableAutoConfiguration//开启自动配置功能（底层使用了1.@AutoConfigurationPackage。作用：将SpingBoot主启动类，及其子包下的内容扫描进Spring容器中。这就是为什么，包不在主启动类及其子包下，是无法扫描进Spring的。2.@Import。将所有需要导入的组件，以全类名的方式，来添加到容器中。这里会给容器中添加96个xxxAutoConfiguartion自动配置类。这些类的作用是导入场景中，需要这个类的的组件，并自动配置好组件。所有这些自动配置类，都被LoaderClass加载为一个Properties对象，然后springboot来通过Spring-boot-autoconfigure这个包下/META-INF/spring.factories，读到这些自动配置类的路径，然后在这个jar包下，也封装有所有spring封装的自动配置类。）
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
````

#### 二、SpringBoot中yml中配置相关

①：将类的属性和yml中的配置绑定@ConfigurationProperties（prefix=“”）

②：此类必须是spring中的组件@Component

③：导入spring‐boot‐configuration‐processor包，配置文件可以有提示

④：如果不需要pojo类和配置文件一一对应，只需要某个配置引入，可以使用@value

⑤：@PropertySource（value={calsspath:xxx.properties}）

导入某个位置的配置文件

⑥：@ImportResource(locations = {"classpath:beans.xml"})这个注解需要添加在配置类上（比如主启动类），然后可以将这个组件导入到spring中

⑦：SpringBoot推荐使用@Configuration来声明一个配置类，@Bean标记到方法上，方法的返回值添加到Spring容器中，bean的id就是方法名

⑧：SpirngBoot会从默认的位置加载多个配置文件，互相之间形成互补配置来同时作用于工程

#### 三、自动配置的原理

SpringBoot启动后，自动配置程序运行如下：

①：@SpringBootApplication此注解下有两个重要注解

②：@SpringBootConfiguration（声明这是一个springboot的配置类）

@EnableAutoConfiguration此注解下有两个重要注解

@AutoConfigurationPackage（标识扫描此包，及包路径下的所有类，添加到spring容器中）

@Import({EnableAutoConfigurationImportSelector.class})加载所有包META-INF/spring.factories的资源，找到EnableAutoConfiguration.class类对应的值，然后把对应的内容添加到容器中。

```properties
# Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
org.springframework.boot.autoconfigure.logging.AutoConfigurationReportLoggingInitializer

# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.autoconfigure.BackgroundPreinitializer

# Auto Configuration Import Listeners
org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener

# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
org.springframework.boot.autoconfigure.condition.OnClassCondition

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
```

加载很多这种类，	

③：以HttpEncodingAutoConfiguration为例，来说明自动配置的过程

```java
@Configuration//标注这是一个配置类
@EnableConfigurationProperties({HttpEncodingProperties.class})//为HttpEncodingProperties启用ConfigurationProperties功能（见下面的代码），将配置文件中对应值，和HttpEncodingProperties绑定起来
@ConditionalOnWebApplication//判断是不是web工程
@ConditionalOnClass({CharacterEncodingFilter.class})//判断当前项目有没有CharacterEncodingFilter这个类；这个是SpringMVC解决乱码的过滤器
@ConditionalOnProperty(//判断配置文件中是否存在spring.http.encoding为前缀的配置
    prefix = "spring.http.encoding",
    value = {"enabled"},
    matchIfMissing = true//true表示即使没有配置，这个配置也生效
)
public class HttpEncodingAutoConfiguration {
    //这个对象中的值，已经和yml映射了
    private final HttpEncodingProperties properties;
	//只有一个有参构造器，这个配置的值，只会从容器中拿
    public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
        this.properties = properties;
    }

    @Bean//给容器中添加一个组件，这个组件的某些属性，需要从properties中获取（properties又会有许多默认配置，还会把yml中配置的属性，加到properties中）
    @ConditionalOnMissingBean({CharacterEncodingFilter.class})
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
        return filter;
    }

```

④：所有配置类中，都有一个这样的xxxProperties类，这个类负责读取yml中，你配置的属性，yml中SpringBoot所提供的所有配置，都在这里体现，如果你没有配置，这个类，会为你加上这个组件的默认值。如果你配置了，他会从yml中，读取你的配置，来覆盖这里的默认设置。

```java
@ConfigurationProperties(
    prefix = "spring.http.encoding"
)//从配置文件中，获取指定前缀的值，来和bean绑定
public class HttpEncodingProperties {
    public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");
    private Charset charset;
    private Boolean force;
    private Boolean forceRequest;
    private Boolean forceResponse;
    private Map<Locale, Charset> mapping;
```

##### 自动配置总结：

SpringBoot所有自动配置的组件，都有xxxAutoConfiguration类来做自动配置，每个组件，会对应一个xxxproperties类，来配置默认的属性，xxxProperties类会读取yml中，用户的配置，来覆盖默认配置，最后，将这些组价都添加到Spring容器中

还有一点需要注意：

​	@Conditional：这个注解是Spring底层的注解，是用来做条件判断的，作用是返回true/false，来控制@Conditional注解标注的类，是否生效。SpringBoot对@Conditional这个注解做了许多扩展。

因为有@Conditionalxxx这些注解的存在，使得，虽然SpringBoot的自动配置类很多，但是不是每一个都生效。想查看某个类是否被加载，yml中配置debug=true。

#### 四、静态资源的映射

①：spring.resource：和静态资源关的设置

②：SpringBoot默认配置了可以使用webjars来管理前端资源（css/js），可以在maven引入依赖后，使用classpath:/META-INF/resources/webjars/这个路径来访问到这些webjars资源

#### 五、Thymeleaf

修改SpringBoot的默认版本，在pom中修改properties标签，将你要使用的版本，添加进去，覆盖默认的版本

默认加载的路径：在classpath:/templates/

th：可以使用任意属性，替换原有属性（比如th：id可以替换原有id）

表达式：OGNL表达式

①：${}来获取变量

②：可以使用jsp的9大内置对象

③：可以使用内置的一些工具对象

#### 六、SpringBoot对SpringMVC的自动配置

可以直接在spring容器中添加@bean，来添加到spring容器中，来实现SpringMVC的定制。例：可以自己实现一个ViewResolver（视图解析器，根据方法返回一个view对象（view来决定是转发/重定向））把这个类加上@bean注解，SpringBoot会自动读取你的配置，加入到SpringMVC的配置中，可以直接使用。

#### 七、如何修改SpringBoot的默认配置

①：SpringBoot的大量xxxAutoConfiguration类中，在自动配置的时候，都使用了@ConditionalOnMissingBean这个注解，这个注解可以判断，在容器中没有当前声明的对象时，才将默认配置，加载到容器中。如果有用户配置好的组件，就直接使用用户配置的组件来覆盖默认配置。如果某些组件，可以有多个，会将默认配置的和用户声明的结合起来（放进一个数组），同时存在。

②：在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置

#### 八、SpringBoot的错误机制

