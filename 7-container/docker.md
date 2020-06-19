# Docker-compose

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

