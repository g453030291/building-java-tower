#### Docker：

build、ship、run

#### 镜像：

使用linux底层的联合文件系统，来实现镜像存储

#### 容器：

本质是一个进程

#### 仓库：

#### HelloWorld：

systemctl start docker：启动docker

docker pull name：拉取远程镜像

docker images：查看本地所有镜像

docker run image：运行一个docker镜像

docker run -d hub.c.163.com/library/nginx：在后台运行

docker ps：查看后台运行所有docker容器

docker exec -it [container id] bash：进入容器内部

docker stop [container id]：停止一个容器

docker run -d -p [主机端口]:（容器端口）[ 容器名]：设置docker容器的端口映射

docker run -d -P [ 容器名]：开放所有端口映射到一个随机端口

#### Linux：

which nginx：查看nginx的位置

#### 问题：

如果遇到找不到ps -ef命令，是因为linux系统不包含ps命令

```shell
#可以先运行
apt-get install procps
#如果依然报错，就执行
apt-get update
#运行成功后，在输入第一条命令运行
```

#### 制作自己的镜像：

Dockerfile：告诉docker制作镜像的步骤

docker build：来执行dockerfile里面的步骤

##### 步骤：

①：新建Dockerfile文件

②：第一行：from 基础镜像名（java程序一般可以使用tomcat的镜像作为起点，也是自带jdk的）

③：MAINTAINER 所有者的名字 联系方式

④：COPY 文件名 tomcat的webapp路径

⑤：使用（docker build -t jpress：latest）来构建镜像

####Docker安装启动mysql：

```shell
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```









CentOS安装Docker：6.8

```shell
1.yum install -y epel-release#先安装docker运行所需要的epel库
2.yum install -y docker-io
3./etc/sysconfig/docker#docker的配置文件
4.service docker start#启动docker后台服务
5.docker version#确认安装成功
```

docker官方文档：https://docs.docker.com/

容器的定义：

Docker利用容器独立运行的一个或一组应用。容器是用镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离、保证安全的平台。可以把容器看作是一个简易版的Linux环境（包括root用户权限、进程空间、用户空间、和网络空间等）和运行在其中的应用程序。容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

Docker运行原理：

![Docker运行原理](https://github.com/g453030291/building-java-tower/blob/master/images/Docker运行原理.png)

Docker和虚拟机的区别：

![Docker和虚拟机的区别](https://github.com/g453030291/building-java-tower/blob/master/images/Docker和虚拟机的区别.png)

镜像定义：（核心就是分层，也就是联合文件系统）

	镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含某个软件所需的所有内容， 包括代码、运行时、库、环境变量和配置文件。

镜像底层使用的是UnionFS（联合文件系统）：

	Union文件系统（Union File System）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一虚拟文件系统下，Union文件系统是Docker镜像的基础。镜像可以通过分层来继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

镜像运行原理：

	Linux系统，底层bootfs来引导，上层是rootfs来实现不同的发行版。比如Ubuntu、Centos底层都是使用Linux的bootfs来引导，上层不同的发行版实现了不同的rootfs。对于一个精简的OSrootfs可以很小，只需要包括最基本的命令、工具和程序就可以了，因为底层直接用宿主机的kernel也就是bootfs，自己只需要提供rootfs就行了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。docker也是共用宿主机的bootfs。

Docker为什么采用这种分层镜像来构建？

	最大的好处就是共享资源。比如多个镜像都从相同的base镜像构建而来，那么宿主机	只需在磁盘上保存一份base镜像。同时内存中也只需加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以共享

Docker镜像特点：

	docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作“容器层”，“容器层”之下的都是”镜像层“。







Docker常用命令：

帮助命令

| 命令           | 作用               |
| -------------- | ------------------ |
| docker info    | docker详细信息描述 |
| docker --help  | docker帮助命令     |
| docker version | 版本               |

镜像命令

| 命令                                  | 作用                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| docker images                         | 本地所有镜像列表                                             |
| docker images -a                      | 列出本地所有镜像（含中间映像层）                             |
| docker images -q                      | 所有本地镜像id（可以将这条命令和上面的a同时使用）            |
| docker images --digests               | 显示镜像的摘要信息                                           |
| docker images --digests --no-trunc    | 显示完整的镜像信息                                           |
| docker search 镜像名                  | 去docker hub查找镜像                                         |
| docker search -s 100 镜像名           | 搜索stars数100以上的镜像                                     |
| docker search --automated             | 只列出automated build类型的镜像                              |
| docker pull 镜像名                    | 下载镜像（此命令会下载默认最新版本 docker pull 镜像名:latest） |
| docker rmi -f 镜像id                  | 删除镜像（-f表示强制删除）                                   |
| docker rmi -f 镜像名1:tag 镜像名2:tag | 删除多个镜像（以空格分开即可）                               |
| docker rmi ${images images -qa}       | 删除本地所有镜像（类似于EL表达式，${}中写的也是docker命令）  |

容器命令

```shell
docker run [OPTIONS] image [COMMAND][ARG]#新建并启动容器
```

OPTIONS说明：

--name=”容器新名字“：为容器制定一个名称

-d：后台运行容器，并返回容器id，也即启动守护式容器

-i：以交互模式运行容器，通常与-t同时使用

-t：为容器重新分配一个伪输入终端，通常与-i同时使用

-P：随机端口映射

-p：指定端口映射，有以下四种格式

	ip:hostPort:containerPort

	ip::containerPort

	hostPort:containerPort

	containerPort

```shell
docker ps [options]#查看后台运行的容器
```

OPTIONS说明：

-a：列出当前所有正在运行的容器+历史上运行过的

-l：显示最近创建的容器

-n：显示最近n个创建的容器

-q：静默模式，只显示容器编号

--no-trunc：不截断输出

| 命令                                       | 作用                     |
| ------------------------------------------ | ------------------------ |
| docker run [OPTIONS] image [COMMAND]/[ARG] | 新建并启动容器           |
| docker ps [options]                        | 查看后台运行的容器       |
| exit                                       | 在伪终端中停止并退出容器 |
| ctrl+p+q                                   | 容器不停止，退出伪终端   |
| docker start 容器id或容器名                | 启动容器                 |
| docker restart 容器名或容器id              | 重启容器                 |
| docker stop 容器名或容器id                 | 温柔停止容器             |
| docker kill 容器名或容器id                 | 强制停止容器             |
| docker rm 容器名或容器id                   | 删除一个关闭的容器       |
| docker rm -f 容器名或容器id                | 强制删除一个容器         |
| docker rm -f ${docker ps -a -q}            | 批量删除容器             |
| Docker ps -a -q \| xargs docker rm         | 批量删除容器             |

容器重要命令：

| 命令                                      | 作用                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| docker run -d 容器名                      | 以后台模式启动一个容器                                       |
| docker log -f -t --tail 容器id            | 查看容器日志                                                 |
| docker top 容器id                         | 查看容器内运行的进程                                         |
| docker inspect 容器id                     | 查看容器内部细节                                             |
| docker exec -it 容器id bashShell          | 在容器外让容器内执行bashShell命令，并返回结果（不进入容器）（在容器中打开新的终端，并且可以启动新的进程） |
| docker attach 容器id                      | 进入容器内部，不会启动新的进程                               |
| docker cp 容器id：容器内路径 目的主机路径 | 从容器内复制文件到宿主机                                     |

docker容器两种启动方式：

1.启动交互式容器：docker -it 容器名

2.启动守护式容器：docker run -d 容器名

```shell
docker run -d centos
```

问题：使用docker ps -a进行查看，会发现容器已经退出。很重要说明的一点：Docker容器后台运行，就必须有一个前台进程。容器运行的命令，如果不是那些一直挂起的命令（比如top，tail）就是会自动退出的。

这个是docker机制的问题，比如web容器nginx为例，正常情况下，我们配置启动服务只需要启动相应的service即可。例如service nginx start。

但是这样做，nginx为后台进程模式运行，就导致docker前台没有运行的应用。这样的容器后台启动后，会立即自杀因为他觉得没事可做了。所以，最佳的解决方案是，将你要运行的程序以前台进程的形式运行。



docker log -f -t --tail 容器id

-t：加入时间戳

-f：跟随最新的日志打印

--tail 数字：数字显示最后多少条



docker制作自定义镜像：

```shell
docker commit -a="gms" -m="tomcat without docs" 9fa4193e6e9a lwj/mytomcat:1.2
```

-a：作者

-m：描述信息

镜像ID 包名/新镜像名：版本号



容器数据卷：

Docker理念：将运用与运行的环境打包形成容器运行，运行可以伴随着容器，但是我们对数据要求是希望持久化的

容器之间希望有可能共享数据

Docker容器产生的数据，如果不通过docker commit生成新的镜像，使得数据做为镜像的一部分保存下来，那么当容器删除后，数据自然也就没有了。

为了能保存数据在docker中我们使用卷。容器数据卷类似于Redis的rdb和aof。负责容器的持久化、容器见继承+共享的数据。

概念：卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性。

卷的设计目的就是数据的持久化，完全独立于容器的生命周期，因此Docker不会在容器删除时删除其挂载的数据卷。

特点：

1.数据卷可在容器之间共享或重用数据

2.卷中的更改可以直接生效

3.数据卷中的更改不会包含在镜像的更新中

4.数据卷的生命周期一直持续到没有容器使用它为止

在容器中添加数据卷：（登陆docker中的centos，docker run -it imagename /bin/bash）

1.直接命令添加

```powershell
docker run -it -v /宿主机绝对路径:/容器内目录 镜像名
```

可以使用docker inspect 容器id，来查看返回的json串，如果有volume和bing信息，说明绑定成功

示例：

```shell
docker run -it -v /dataVolume:/dataVolumeContainer:ro centos
ro：read only表示只读
```

注意：这里如果出现了数据卷不能读写的情况，可以在命令后，加上 --privileged=true。直接授权读写权限即可。

2.docker file添加

```shell
最简单的docker file模版示例
# volume test
FROM centos
VOLUME["/dataVolumeContainer1","dataVolumeContainer2"]
CMD echo "finished,--------success1"
CMD /bin/bash
```

```shell
docker build命令示例
docker build -f /mydocker/Dockefile -t gms/centos .
```

Docker file不能指定宿主机的对应共享文件位置，docker会将images生成container后，默认会指定共享文件的位置，使用docker insepact

数据卷容器：

命名的容器挂载数据卷，其他容器通过挂在这个（父容器）实现数据共享，挂载数据卷的容器，称之为数据卷容器

```shell
docker run -it --name dc02 --volumes-from dc01 gms/centos
#dc02的容器数据卷继承自dc01
#继承的目录其实是共享的，父子文件可以互相传递
```

结论：容器间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止。

Docker File解析：

概念：Dockerfile是用来构建docker镜像的构建文件，是由一系列命令和参数构成的脚本

构建一个docker image步骤：docker file、docker build、docker run

```shell
#centos的docker file示例：
FROM scratch#所有docker镜像的祖先，相当于object
MAINTAINER The CentOS Project <cloud-ops@centos.org>
ADD c68-docker.tar.xz /
LABEL name="CentOS Base Image" \
    vendor="CentOS" \
    license="GPLv2" \
    build-date="2016-06-02"

# Default command
CMD ["/bin/bash"]
```

Dockerfile内容基础知识：

1.每条保留字指令都必须为大写字母且后面要跟随至少一个参数

2.指令从上到下，顺序执行

3.#表示注释

4.每条指令都会创建一个新的镜像层，并对镜像进行提交

Docker执行dockerfile的大致流程：

1.docker从基础镜像运行一个容器

2.执行一条指令并对容器做出修改

3.执行类似docker commit的操作提交一个新的镜像层

4.docker再基于刚提交的镜像运行一个容器

5.执行dockerfile中的下一条指令直到所有的指令都执行完成

![Docker file:images:container](https://github.com/g453030291/building-java-tower/blob/master/images/Docker file:images:container.png)

从应用软件的角度看，Dockerfile、Dockerimage、Dockercontainer分别代表软件的三个不同阶段

Dockerfile时软件的原材料

Dockerimage时软件的交付品

Dockercontainer则可以认为是软件的运行状态

Dockerfile面向开发，Dockerimage成为交付标准，Dockercontainer则涉及部署与运维，三者缺一不可。

Docker保留字指令：

| 保留字     | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| FROM       | 基础镜像，当前将要编写的镜像基于哪个镜像                     |
| MAINTAINER | 镜像维护者的姓名和邮箱地址                                   |
| RUN        | 容器构建时需要运行的命令                                     |
| EXPOSE     | 当前容器对外暴漏出的端口                                     |
| WORKDIR    | 指定在创建容器后，终端默认登陆进来的工作目录                 |
| ENV        | 用来构建镜像过程中设置环境变量                               |
| ADD        | 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包 |
| COPY       | 类似ADD，拷贝文件和目录到镜像中。将从构建上下文目录中<源路径>的文件/目录复制到新一层的镜像内的<目标路径>位置 |
| VOLUME     | 容器数据卷，用于数据保存和持久化工作                         |
| CMD        | 指定一个容器启动时要运行的命令；Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换 |
| ENTRYPOINT | 指定一个容器启动时要运行的命令；ENTRYPOINT的目的和CMD一样，都是在指定容器启动程序及参数 |
| ONBUILD    | 当构建一个被继承的Dockerfile时运行命令，父镜像在被子继承后父镜像onbuild触发 |

ENV:

```shell
ENV MY_PATH /usr/mytest
#这个环境变量可以在后续的人和RUN指令中使用，就如同在命令前面指定了环境变量前缀一样；
#也可以在其他指令中直接使用这些环境变量
比如：WORKDIR $MY_PATH
```

CMD容器启动命令：（只有最后一个CMD的linux命令会生效）

CMD指令的格式和RUN相似，也是两种格式：

- shell格式：CMD<命令>

- exec格式：CMD["可执行文件","参数1","参数2"...]
- 参数列表格式：CMD["参数1","参数2"...]，在指定了EWTRYPOINT指令后，用CMD指定具体的参数

案例：

Base镜像（scratch）：Docker Hub中99%的镜像都是通过在base镜像中安装和配置需要的软件构建出来的

自定义镜像示例：

1.编写dockerfile

```shell
FROM centos
MAINTAINER gms<453030291@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "success------------ok"
CMD /bin/bash
```

2.构建

```shell
docker build -f /mydocker/Dockerfile02 -t mycenteros:1.3 .
#最后的点.表示当前路径
```

3.运行

```shell
docker run -it mycentos:1.3
```

4.列出镜像的变更历史

```shell
docker history 431eb4092d06
```

CMD和ENTRYPOINT镜像案例：

查询ip镜像示例：

```shell
FROM centos
RUN yum install -y curl
ENTRYPOINT ["curl","-s","http://ip.cn"]#这里既可以使用CMD也可以使用ENTRYPOINT，使用CMD的话，不能添加其他的参数。相当于覆盖了前一条CMD。如果使用的是ENTRYPOINT，就可以添加其他参数
```

综合示例自定义镜像：tomcat

```shell
1.mkdir -p /gms/mydockerfile/tomcat
2.touch c.txt
3.准备镜像需要的文件（c.txt、tomcat、jdk）
  cp /opt/apache-tomcat-9.0.8.tar.gz
  cp /jdk-8u171-linux-x64.tar.gz
4.vim Dockerfile 
5.
FROM centos
MAINTAINER gms<453030291@qq.com>
#把宿主机当前上下文的c.txt拷贝到容器/usr/local/路径下
COPY c.txt /usr/local/cincontainer.txt
#把java与tomcat添加到容器中
ADD dk-8u171-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.8.tar.gz /usr/local/
#安装vim编辑器
RUN yum -y install vim
#设置工作访问时候的WORKDIR路径，登陆落脚点
ENV MYPATH /usr/local
WORKDIR $MYPATH
#配置java与tomcat环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_171
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.8
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.8
ENV PATH $PATH:JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
#容器运行时监听的端口
EXPOSE 8080
#启动时运行tomcat
#ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh"]
#CMD ["/usr/local/apache-tomcat-9.0.8/bin/catalina.sh","run"]
CMD /usr/local/apache-tomcat-9.0.8/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.8/bin/logs/catalina.out

6.docker build -t gmstomcat9 .#不写-f加文件名，就是默认读取当前路径下Dockerfile文件
7.docker images
8.docker run -d -p 9080:8080 --name myt9 -v /gms/mydockerfile/tomcat9/test:/usr/local/apache-tomcat-9.0.8/webapps/test
-v /gms/mydockerfile/tomcat9/tomcat9logs/:/usr/local/apache-tomcat-9.0.8/logs --privileged=true gmstomcat9
9.验证
docker exec 038cad905302 java -version
eocker exec 038cad905302 ls -l
```

Docker常用安装：

mysql：

```shell
1.docker search mysql
2.docker pull mysql:5.6
3.docker run -p 12345:3306 --name mysql -v /gms/mysql/conf:/etc/mysql/conf.d -v /gms/mysql/logs:/logs -v /gms/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
4.docker exec -it ba194da55684 /bin/bash
5.mysql -uroot -p 
6.show databases
7.导出docker内mysql的数据
docker exec ba194da55684 sh -c ' exec mysqldump --all-databases -uroot -p"123456" > /gms/all-databases.sql
```

Reis:

```shell
1.docker pull redis:3.2
2.docker run -p 6379:6379 -v /gms/myredis/data:/data -v /gms/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf -d redis:3.2 redis-server /usr/local/etc/redis/redis.conf --appendonly yes
3.vim /gms/myredis/conf/redis.conf/redis.conf#编写reids的配置文件
4.docker exec -it ce38c8665cd5 redis-cli
```

自定义镜像推送到阿里云：

```shell
docker run -it mycentos:1.3
docker ps
docker vommit -a gms -m "new mycentos with vim and ifconfig" d2f590e000b2 mycentos:1.4
```

centos7安装dockder：

```shell
#安装gcc
yum -y install gcc
yum -y install gcc-c++
#确认gcc安装成功
gcc -v
#参考官网命令，卸载旧的docker版本
#安装需要的软件
yum install -y yum-utils device-mapper-persistent-data lvm2
#设置stable镜像仓库（这里不要去连接国外的docker网站下载，要使用国内镜像）
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#查看执行结果
cat /etc/yum.repos.d/docker-re.repo
#更新yum软件包的索引
yum makecache fast
#安装docke ce
yum -y install docker-ce
#启动docker
systemctl start docker
#测试
docker version
docker run hello-world
#配置镜像加速
mkdir -p /etc/docker
vim /etc/docker/daemon.json
systemctl daemon-reload
systemctl restart docker
#查看docker启动状态
ps -ef|grep docker|grep -v grep
#卸载
systemctl stop docker 
yum -y remove docker-ce
rm -rf /var/lib/docker
```

