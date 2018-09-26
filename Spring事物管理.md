#### Spring事物机制：

##### 内容：

Spring事物机制：事物抽象、事物传播、事物隔离

代码方式与标签方式的事物实现

JPA、JMS事物实例

##### Spring事物管理：

提供统一的API接口支持不同资源

提供声明式事物管理

方便的与Spring框架集成

多个资源的事物管理、同步

##### Spring事物抽象：

PlatformTransactionManager：事物管理器的接口

![Spring事物管理器.png](https://github.com/g453030291/building-java-tower/blob/master/images/Spring事物管理器.png)

![Spring事物隔离级别.png](https://github.com/g453030291/building-java-tower/blob/master/images/Spring事物隔离级别.png)

![Spring事物传播机制.png](https://github.com/g453030291/building-java-tower/blob/master/images/Spring事物传播机制.png)

TransactionDefinition：事物的定义接口

![Spring-TransactionDefinition.png](https://github.com/g453030291/building-java-tower/blob/master/images/Spring-TransactionDefinition.png)

TransactionStatus：正在运行的事物状态

![Spring-TransactionStatus.png](https://github.com/g453030291/building-java-tower/blob/master/images/Spring-TransactionStatus.png)



