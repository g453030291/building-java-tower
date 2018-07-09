#### Rancher：

管理docker的工具

#### 安装：

①：先配置镜像地址

（因为rancher有1G+的大小，所以使用国外地址非常慢，需要配置国内的镜像加速器）

vim /etc/docker/daemon.json

```shell
{
  "registry-mirrors":["https://fy707np5.mirror.aliyuncs.com"]
}
```

systemctl daemon-reload
systemctl restart docker

②：再到racher官网，找到下载racher的命令，运行

```shell
sudo docker run -d --restart-unless-stopped -p 8080:8080 rancher/server:stable
```

③：查看racher是否启动成功（docker ps）

④：进入racher控制台，http://192.168.1.103:8080

⑤：先添加一台主机，就将本台docker添加上去

⑥：再新增一台主机，作为racher的agent端，安装docoer（yum install docker）

⑦：启动docker（systemctl start docker）

⑧：将agent端的ip地址，填入racher-server端的添加主机页面，复制脚本，到agent端运行（这里可能会很慢，需要先设置一下镜像地址再进行这步操作）

⑨：构建镜像打包推送到网易云镜像仓库：

```shell
#!/usr/bin/env bash

mvn clean package -Dmaven.test.skip=true -u

docker build -t hub.c.163.com/springcloud/eureka .

docker push hub.c.163.com/springcloud/eureka
```

