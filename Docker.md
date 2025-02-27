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

* windows
  
  setting > docker engine > 添加到json的后面即可
  
  重启docker


