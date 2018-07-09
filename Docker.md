#### Docker：

build、ship、run

#### 镜像：

使用linux底层的联合文件系统，来实现镜像存储

#### 容器：

本质是一个进程

#### 仓库：

#### HelloWorld：

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

