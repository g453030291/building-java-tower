### SpringCloudStream

功能：

是springcloud对mq的进一步封装，目前只支持RabbitMQ和kafka，他可以做到代码对mq无感知，甚至可以动态切换mq。

可以使用spring.cloud.stram来分组，实现多个client监听一个队列，避免一条消息被消费两次

加上content-type:application/json在rabbitMQ中可以以json格式查看

@SendTo():消息消费后，消费者向发送者返回结果

创建队列，应该是在消费端使用@rabbitListener接受消息，创建队列