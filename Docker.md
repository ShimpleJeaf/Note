# 镜像和容器

镜像是镜像，容器是容器，镜像只有一个，在同一个镜像的基础上，可以运行多个容器。

不同容器使用不同的名字区分，建立容器时使用--name参数进行指定

```batch
docker run -d --name bitmagnet-next-web journey0ad/bitmagnet-next-web:latest
```

# 容器内使用本机网络

使用host.docker.internal进行本机web服务访问

# 启动指定WSL系统

```batch
wsl -d docker-desktop
```

# 端口映射

-p 

支持的格式有3种：

1. IP:HostPort:ContainerPort

2. IP:ContainerPort

3. HostPort:ContainerPort

将主机端口映射到容器端口

# 修改镜像源

* vi /etc/docker/daemon.json
  
  没有则root用户新建

* 添加镜像
  
  ```json
  {
    "registry-mirrors": [
      "[https://docker.m.daocloud.io"](https://docker.m.daocloud.io"),
      "[https://noohub.ru"](https://noohub.ru"),
      "[https://huecker.io"](https://huecker.io"),
      "[https://dockerhub.timeweb.cloud"](https://dockerhub.timeweb.cloud"),
      "[https://0c105db5188026850f80c001def654a0.mirror.swr.myhuaweicloud.com"](https://0c105db5188026850f80c001def654a0.mirror.swr.myhuaweicloud.com"),
      "[https://5tqw56kt.mirror.aliyuncs.com"](https://5tqw56kt.mirror.aliyuncs.com"),
      "[https://docker.1panel.live"](https://docker.1panel.live"),
      "http://mirrors.ustc.edu.cn/",
      "http://mirror.azure.cn/",
      "https://hub.rat.dev/",
      "https://docker.ckyl.me/",
      "[https://docker.chenby.cn"](https://docker.chenby.cn"),
      "[https://docker.hpcloud.cloud"](https://docker.hpcloud.cloud"),
      "[https://docker.m.daocloud.io"](https://docker.m.daocloud.io") 
    ]
  }
  ```

* 重启docker服务
  
  systemctl restart docker

* Windows
  
  setting > docker engine > 添加到json的后面即可
  
  重启docker

# Windows一些报错

* 报错不支持WSL2
  
  启用Windows虚拟化组件
  
  管理员身份运行
  
  ```batch
  bcdedit /set hypervisorlaunchtype auto
  ```
  
  重启

* Windows Docker不需要安装任何linux分发，只需要安装必要组件
  
  ```batch
  wsl --install --no-distribution
  ```

# Postgre数据库URL格式

```url
postgresql://username:password@host:port/databasename
```
