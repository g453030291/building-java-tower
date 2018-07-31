# RabbitMQ

#### 消息发布的两种方式

①点对点式：

​	消息发送者发送消息，消息代理将其放入一个队列中，消息接收者从队列中获取消息内容，消息读取后被移除队列。

​	消息只有唯一的发送者和接受者，但并不是说只能有一个接受者。（可以有多个消费者来消费，但是只能有一个消费者消费成功，一旦消息被消费后，就会删除。后来的消费者无法消费。）

②发布订阅式：

​	发送者发送消息到一个主题（topic），多个接受者监听这个主题，那么就会在消息到达时同时接受消息。

#### 两种消息服务的规范

JMS（Java Message Server）JAVA消息服务：java api

​	基于JVM消息代理的规范

AMQP（Advanced Message Queuing Protocol）：网络协议

​	高级消息队列协议，兼容JMS

#### 核心概念

Message：

​	消息，由消息头和消息体组成。消息体是不透明的，消息头是由一系列属性组成的。包括routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久化）。

Publisher：

​	消息的生产者，也是一个向交换器发布消息的客户端应用程序。

Exchange：

​	交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。

​	Exchange有4种类型：direct(默认)，fanout，topic，和headers，不同类型的Exchange转发消息的策略有所区别。

Queue：

​	消息队列，用来保存消息知道发送给消费者。他是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里，等待消费者连接这个队列将其取走。

Binding：

​	绑定，用于消息队列和交换器之间的关联，一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。

​	Exchange和Queue的绑定可以是多对多的关系。

Connection：

​	网络连接，比如一个TCP连接。

Channel：

​	信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP链接内的虚拟连接，AMQP命令都是通过信道发出去的，不管是发布消息、订阅消息、还是接受消息，这些动作都是通过信道完成的。因为对于操作系统来说建立和销毁TCP连接都是非常昂贵的开销，所以引入信道的概念，以复用一条TCP连接。

Consumer

​	消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。

Virtual Host

​	虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 / 。

Broker

​	表示消息队列服务器实体

#### 运行原理

![RabbitMQ运行原理.png](https://github.com/g453030291/building-java-tower/blob/master/images/RabbitMQ运行原理.png)

​	Producer把消息发送给Broker，由Broker中的Exchange接收到，然后根据Binding规则来放入不同的Queue，然后Consumer来消费。

#### Exchange的4种类型

direct、fanout、topic、headers

direct：点对点模式，消息中的路由键（routing key）和Binding中的binding key匹配，就发送。

fanout：广播模式，不处理路由键，而是将消息简单的发送到所有和该fanout exchange绑定的队列中

topic：将路由键和绑定键的字符切分成单词，使用#和*匹配。#匹配0个或多个单词，星号匹配一个单词。

headers：和direct完全一致，性能差很多，所以不使用。