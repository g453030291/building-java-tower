Redis数据类型：redis内部使用redisObject来表示所有key和value，type表示value是何种类型

#### String

​	value可以是一个整形，使用incr就会使value自动加一，incr是原子操作，不会产生竞争的情况，redis会自动串行的操作

#### List

​	基于LinkedList实现。LPUSH->从头部添加元素;RPUSH->从尾部添加元素;LRANGE->从list中取出一定范围的元素（分页）使用场景：聊天系统，队列，实现生产者消费者模式。优点：可以按照添加顺序访问，不需要sql中的order by

#### Hash

​	传统的存储一个对象，要用id作为key，取出后要反复序列化来转化，带来很大开销，如果想不去转化，可以单个用户使用id+属性名，value存储属性值的方式，但是这样，浪费内存。redis提供了hash的数据格式，key可以存储id，value内部维护一个hashmap，这样对数据的修改和存取都可以直接通过其内部Map的Key(Redis里称内部Map的key为field), 也就是通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了

#### Set

​	可以实现自动排重，提供了判断某个成员是否在一个set集合当中。还提供了求交集、并集、差集的操作

#### Sorted Set

​	可以通过score的参数为成员排序，还可以加入权重，来让重要的任务先执行

redis可以设置key的过期时间

#### Redis数据持久化

##### RDB

​	可设置在每间隔n秒redis有m个key被修改，就做一次自动快照，保存为dump.rdb

优势：1.方便备份；2.恢复速度比AOF快；3.进行RDB操作时，会fork出一个子进程去处理工作

劣势：1.会丢失最后一次记录时间中的数据；2.数据量大时，会占用过多资源，影响主进程

##### AOF

​	将每次执行的命令都记录下来，恢复数据时再重新执行一遍。保存为appendonly.aof

优势：1.redis会将过大的aof文件重写；2.aof默认为每秒重写，保证性能

劣势：1.aof文件体积大于rdb；2.处理大量写入时，rdb会提供更有保证的最大延迟时间

#### redis数据过期策略

##### 过期数据清理时机

​	1.访问key时会判断key是否过期；2.cpu空闲时；3.事件循环执行时逐出过期key

##### 过期清理数据算法

​	Redis过期Key清理的机制对清理的频率和最大时间都有限制，在尽量不影响正常服务的情况下，进行过期Key的清理，以达到长时间服务的性能最优.

Redis会周期性的随机测试一批设置了过期时间的key并进行处理。测试到的已过期的key将被删除。具体的算法如下:

1. Redis配置项hz定义了serverCron任务的执行周期，默认为10，即CPU空闲时每秒执行10次;
2. 每次过期key清理的时间不超过CPU时间的25%，即若hz=1，则一次清理时间最大为250ms，若hz=10，则一次清理时间最大为25ms;
3. 清理时依次遍历所有的db;
4. 从db中随机取20个key，判断是否过期，若过期，则逐出;
5. 若有5个以上key过期，则重复步骤4，否则遍历下一个db;
6. 在清理过程中，若达到了25%CPU时间，退出清理过程;

理解：这是一个基于概率的简单算法，基本的假设是抽出的样本能够代表整个key空间，redis持续清理过期的数据直至将要过期的key的百分比降到了25%以下。这也意味着在长期来看任何给定的时刻已经过期但仍占据着内存空间的key的量最多为每秒的写操作量除以4.

结果：由于算法采用的随机取key判断是否过期的方式，故几乎不可能清理完所有的过期Key;

调高hz参数可以提升清理的频率，过期key可以更及时的被删除，但hz太高会增加CPU时间的消耗;

#### redis内存淘汰策略

Redis的内存淘汰策略是指在Redis的用于缓存的内存不足时，怎么处理需要新写入且需要申请额外空间的数据。

- noeviction：当内存不足以容纳新写入数据时，新写入操作会报错。
- allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key。
- allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key。
- volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key。
- volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key。
- volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除。

#### 其他

修复redis 的错误aof文件：redis-check-aof --fix appendonly.aof

appendonly.aof 和 dump.rdb文件可以共存。redis启动的时候。会加载aof文件

redis对事物的支持：部分支持

自增：incr

开启事务：multi

执行事务：exec 

减少：decrby key 数量

增加：incrby key 数量

锁：watch

![Redis锁、事务.png](https://github.com/g453030291/building-java-tower/blob/master/images/Redis锁和事务.png)

主从复制：slaveof 主库IP 主库端口

查看当前redis配置详情：info replication

#### Redis分布式锁

两条重要命令：

SETNX(set not exists)：如果key不存在，就创建成功，返回1；key存在，创建失败，返回0。

GETSET：先get出，key返回，然后set进去新值

#### Redis实践

在linux环境下Redis可以直接通过源码编译安装。Windows下编译一般不那么方便，我们使用已经编译好的.msi安装包来安装。 首先找到Windows下的Redis安装包 打开官网下载页面找到"Windows"项然后点击“Learn more”,打开github项目 找到Redis安装包并下载 在上一步打开github页面中下拉找到README.md里面的"Redis on Windows"项，这里有一句”You can download the latest unsigned binaries and the unsigned MSI installer from the release page.“.点击”release page“打开下载页面，这里可以选择对应的版本下载。 这里我选择版本3.0.501然后在页面下点击“Redis-x64-3.0.501.msi"下载安装包 3安装Redis 下载Redis-x64-3.0.501.msi之后双击打开安装配置页面，一般来说默认即可，但是你要知道配置项的意思，根据具体情况配置。最后点击install即可安装。 启动Redis 安装之后发现安装目录F:\ProgramFiles\Redis有很多.exe文件。

https://github.com/MicrosoftArchive/redis/releases

启动：redis-server redis.conf
启动客户端：redis-cli.exe -h 127.0.0.1 -p 6379

redis启动不成功:
1. redis-cli.exe
2. shutdown
3. exit


查看redis数据数量：dbsize