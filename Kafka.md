# Kafka

1. AMQP协议：

消费者（Consumer）：向消息队列请求消息

生产者（Produce）：向broker发布消息

AMQP服务器端（broker）：用于接收生产者发送的消息，并将消息路由给服务器中的队列

2. 主题（Topic）：一个主题类似于新闻中的体育、娱乐、教育等分类概念，在实际工程中通常一个业务一个主题

3. 分区（Partition）：一个topic中的消息数据按照多个分区组织，分区是kafka消息队列组织的最小单位，一个分区可以看做是一个FIFO的队列;

4. 备份（Replication）：为了保证分布式可靠性,kafka0.8开始对每个分区的数据进行备份（不同Broker上），防止其中一个Broker宕机造成分区数据不可用

5. 