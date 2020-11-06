# Docker-compose

## 配置阿里镜像加速

国内从 DockerHub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：

- 网易：**https://hub-mirror.c.163.com/**
- 阿里云：**https://<你的ID>.mirror.aliyuncs.com**
- 七牛云加速器：**https://reg-mirror.qiniu.com**

当配置某一个加速器地址之后，若发现拉取不到镜像，请切换到另一个加速器地址。国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker 的云平台选择对应的镜像加速服务。

阿里云镜像获取地址：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors，登陆后，左侧菜单选中镜像加速器就可以看到你的专属地址了：

![img](C:\Users\12605\Desktop\PHP_notes\.img\02F3AF04-8203-4E3B-A5AF-96973DBE515F.jpg)

### Ubuntu14.04、Debian7Wheezy

对于使用 upstart 的系统而言，编辑 /etc/default/docker 文件，在其中的 DOCKER_OPTS 中配置加速器地址：

```
DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"
```

重新启动服务:

```
$ sudo service docker restart
```

### Ubuntu16.04+、Debian8+、CentOS7

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）：

```
{"registry-mirrors":["https://reg-mirror.qiniu.com/"]}
```

之后重新启动服务：

$ **sudo** systemctl daemon-reload
$ **sudo** systemctl restart docker

### Windows 10

对于使用 Windows 10 的系统，在系统右下角托盘 Docker 图标内右键菜单选择 Settings，打开配置窗口后左侧导航菜单选择 Daemon。在 Registrymirrors 一栏中填写加速器地址 **https://registry.docker-cn.com** ，之后点击 Apply 保存后 Docker 就会重启并应用配置的镜像地址了。

![img](C:\Users\12605\Desktop\PHP_notes\.img\20190610104724909.png)

## 部署一个wordpress

- mysql容器

```shell
$ docker run -d --name mysql_tmp -v ./lnmp -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=wordpress mysql
#-v设置持久存储
$ docker run -d -e WORDPRESS_DB_HOST=mysql_tmp:3306 --link mysql -p 8079:80 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=root -e WORDPRESS_DB_NAME=wordpress -e WORDPRESS_DEBUG=1 wordpress
#设置换变量 连接mysql容器 映射到本地8079端口
```

```shell
docker run -it --rm --name my-running-app my-php-app

docker run --name some-nginx -p 8088:80 -v "//C/Users/12605/Desktop/docker/blog":/usr/share/nginx/html:ro -d -v "//C/Users/12605/Desktop/docker/ncof":/etc/nginx/nginx.conf:ro nginx

docker exec -it some-nginx /bin/bash

docker run --name some-nginx -p 8088:80 -d nginx

docker cp some-nginx:/etc/nginx/nginx.conf "C:\Users\12605\Desktop\docker\ncof"

docker cp "C:\Users\12605\Desktop\docker\ncof" some-nginx:/etc/nginx/nginx.conf 

docker run -ti -p 9501:9501 --name es-1 -v /easyswoole:C：Users/12605/Desktop/docker/docker-swoole/es:ro es

docker cp es:/easyswoole "C:\Users\12605\Desktop\docker\docker-swoole\es" 
```

- 设置windows环境下挂载![image-20200616115000750](https://i.loli.net/2020/06/16/vB1qYtEkxbhDnCz.png)

```shell
$ docker run --name some-nginx -p 8088:80 -v C:/Users/12605/Desktop/docker/blog:/usr/share/nginx/html/blog:rw  -v C:/Users/12605/Desktop/docker/ncof:/etc/nginx/nginx.conf:ro -d nginx
```



## compose概念

### 概念

- 多容器app**批处理**

- Docker Compose是一个工具
- 这个工具可以通过一个yml文件定义多容器的docker应用
- 通过一条命令就可以根据yml文件的定义去创建或者管理这多个容器

### docker-compose.yml

<img src="https://i.loli.net/2020/06/15/qaZtj8VdJz7MiyO.png" alt="image-20200615205133996" style="zoom:50%;float:left" />

- Services
  - 一个service代表一个container ,这个container可以从dockerhub的image来创建, 

    或者从本地的Dockerfilebuild出来的image来创建

  - Service的启动类似docker run ,我们可以给其指定network和volume ,所以可以给service指定network和
    Volume的引用

- Volumes

  ![image-20200615205712577.png](https://i.loli.net/2020/06/15/vrfDRP8JVWiusnS.png)

- Networks

  <img src="https://i.loli.net/2020/06/15/vrfDRP8JVWiusnS.png" alt="image-20200615205712577" style="zoom:50%;float:left" />

