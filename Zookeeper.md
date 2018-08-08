# Zookeeper

#### 功能

节点选取(选取master节点，从节点可以实时替代宕机的主节点)

统一管理配置文件

发布/订阅

分布式锁

集群管理:保证数据的强一致性

#### 常用命令

ls：查看目录下的文件（类似linux的LL）

ls2：相当于ls+stat

Get：获取节点下子节点

Stat：当前节点状态

Creat:

Set:修改

#### Session

客户端与服务端之间的连接存在会话

每个会话都可以设置一个超时时间

心跳结束：session过期

Session过期，则临时节点znode就会抛弃

心跳机制，客户端向服务端的ping包请求

#### Watcher机制

针对每个节点的操作，都会有一个监督者---watcher(可以理解为触发器)

当监控的某个对象（znode）发生了变化，则触发watcher事件

Zookeeper中，watcher是一次性的，触发后立即销毁

父节点、子节点，增删改都会触发watcher事件

针对不同的操作(增删改)，触发的watcher事件也不同

##### 父节点Watcher事件

创建父节点：触发nodecreat

修改父节点：nodedatachanged

删除父节点：nodeDeleted

##### 子节点watcher事件

Ls为父节点设置watcher，创建子节点触发nodechildrenchanged

Ls为父节点设置watcher，删除子节点触发nodechildrenchanged

Ls为父节点设置watcher，修改子节点不触发事件

##### Watch使用场景

统一资源配置

#### ACL（access control lists）权限

针对节点可以设置相关读写等权限，目的为了保障数据安全性

权限permission可以指定不同的权限范围以及角色

#### ACL命令

getACL：获取某个节点acl信息

setACL：设置某个节点的acl信息

Addauth：输入认证授权信息，注册时输入明文密码登录，但是在zookeeper系统中，密码是以加密形式存在的

#### Zookeeper三要素

Watcher机制(一次性的临时watcher和永久的watcher)

Node(节点)状态：临时、持久、有序等4种

ACL(权限)

详细的节点状态：乐观锁（version验证）