# 一、Swoole基础

## 简介

### 1. Swoole是什么

- PHP异步网络通信引擎
- 最终编译为<kbd>.so文件</kbd>作为PHP的扩展
  
### 2. [特性](https://www.swoole.com/)
 - Swoole使用纯C语言编写，提供了PHP语言的<font color =red>**异步多线程**👍</font>服务器，**异步TCP/UDP网络**📶客户端，异步MySQL，异步Redis，数据库连接池，AsyncTask，消息队列，毫秒定时器，异步文件读写，异步DNS查询。Swoole内置 了Http/WebSocket服务器端/客户端、Http2.0服务器端。

 - 除了异步I/O的支持之外，Swoole 为PHP多进程的模式设计了多个并发数据结构和IPC通信机制，可以大大简化多进程并发编程的工作。其中包括了并发原子计数器，并发HashTable，Channel， Lock， 进程间通信IPC等丰富的功能特性。

 - Swoole2.0支持了类似Go语言的协程，可以使用完全同步的代码实现异步程序。PHP 代码无需额外增加任何关键词，底层自动进行协程调度，实现异步。

### 3.实际应用

- YY  
- 虎牙
- 战旗

## 准备工作

### 1.环境

- linux环境下开发
- <font color=red>php7    swoole2.1    redis</font>
- <font color=red>源码安装php7   源码安装swoole</font>

### 2.PHP7源码编译安装

[php官网](https://www.php.net/downloads) 下载tag.bz2

1. **解压**  

   ```bash
   $ tar -xjvf (包名)
   ```

2. **configure预编译** <font color=red>检查安装环境</font>   需要GCC和pkg-config 以及 libxml2-dev

   ```bash
   sudo apt install gcc -y  &&
   sudo apt install g++ -y &&
   sudo apt install make -y &&
   sudo apt install openssl -y &&
   sudo apt install curl -y &&
   sudo apt install libbz2-dev -y &&
   sudo apt install libxml2-dev -y &&
   sudo apt install libjpeg-dev -y &&
   sudo apt install libpng-dev -y &&
   sudo apt install libfreetype6-dev -y &&
   sudo apt install libzip-dev -y &&
   sudo apt install libssl-dev -y &&
   sudo apt install libsqlite3-dev -y &&
   sudo apt install libcurl4-openssl-dev -y &&
   sudo apt install libgmp3-dev -y &&
   sudo apt install libonig-dev -y &&
   sudo apt install libreadline-dev -y &&
   sudo apt install libxslt1-dev -y &&
   sudo apt install libffi-dev -y &&
   sudo apt install build-essential -y
   ```
   
   ```shell
   $ yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel
    sqlite-devel
   ```
   
   
   
   ```bash
   $ ./configure --help #查看命令
   $ ./configure --prefix=/home/soft/php \
   --enable-fpm --with-mysql \
   --with-mysqli --with-pdo-mysql \
   --with-openssl --enable-debug \
   --with-curl#指定安装路径
   ```
   
3. **make **编译构建

4. **make install** 安装

   ```shell
   $ make -j && make install
   ```

5. 配置PHP**环境PATH**

   ```bash
   $ alias php=/home/kiyo/study/soft/php/bin/php
   ```

   或者

   ```shell
   $ vi ~/.profile
   
   #修改以下路径
   #export PATH
   #alias php=/home/kiyo/study/soft/php/bin
   
   $ source ~/.bash_profile
   $ php -v
   ```

6. 自定义路径下**配置文件** 解压文件目录下有：**php.ini**-development php.ini-production 

   ```shell
   $ cp php.ini-development  /home/name/soft/php/bin/php/etc #注意目录位置
   $ mv php.ini-development php-ini #改名
   $ php -i |grep php.ini #查看文件位置
   $ mv ./etc/php.ini ./lib/ #根据配置，可能需要移动位置
   $ cp ./etc/php-fpm.d/www.conf.default ./etc/php-fpm.d/www.conf
   ```




### 3.安装Swoole

1. [github上](https://github.com/swoole/swoole-src) 克隆  或者  下载zip并解压 unzip

   ```shell
   $ git clone https://github.com/swoole/swoole-src.git
   ```

2. 使用***phpize***生成configure文件  编译安装

   ```shell
   $ /home/kiyo/study/soft/php/bin/phpize #在该路径下生成
   $ ./configure --with-php-config=/home/kiyo/study/soft/php/bin/php-config
   $ make -j && make install
   #进入安装目录可以发现swoole.so
   Installing shared extensions:     /home/kiyo/study/soft/php/lib/php/extensions/no-debug-non-zts-20190902/            Installing header files:          /home/kiyo/study/soft/php/include/php/ 
   ```

3. 源码目录下试用Demo    /swoole-src-master/examples

   ```shell
   $ php echo.php #发现报错
   
   $ php -m # 查看模块
   
   $ vi /php/lib/php.ini #修改文件 加入如下字段
   extension=swoole
   
   $ php -m # 查看模块
   [PHP Modules]                                                       swoole 
   $ netstat -anp|grep 9501 #查看端口占用 wsl与windows共用端口
   $ netstat -anp|findstr 9501 #cmd中使用
   ```

   到此已经正确启用swoole拓展

### 4.nginx 源码安装

[nginx官网](https://nginx.org/en/download.html)下载安装包解压

[安装方法](https://www.php.net/manual/zh/install.unix.nginx.php)

$ ./configure --prefix=/home/kiyo/study/soft/nginx #指定安装路径

### 5 . yum版安装

- ###### nginx的安装

  [yum源修改](https://www.imooc.com/video/18203)

  ```bash
  $ man yum #查看说明
  $ cd /etc/yum.repos.d/ #修改yum源
  
  $ yum search nginx
  ```

  创建nginx的rep(https://nginx.org/en/linux_packages.html)

  ```bash
  $ sudo yum install nginx #安装
  
  $ whereis nginx #查看位置
  
  $ nginx #启动
  ```

  阿里云需要配置安全组规则才能看到页面

- php的安装

  ```bash
  $ rpm -qa | grep php
  $ yum remove php* #删除所有php相关包
  
  #添加源
  $ rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm  
  $ rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm  
  
  $ yum search php7
  ```

  

## Swoole网络通信引擎的使用

### 1.TCP服务器

- 创建服务器

    ```php
    //创建Server对象，监听 127.0.0.1:9501端口
    $serv = new Swoole\Server("127.0.0.1", 9501); 

    //设置运行时的各项参数
    $serv->set(array(
        'reactor_num'   => 2,     // reactor thread num
        'worker_num'    => 4,     // worker process num
        'backlog'       => 128,   // listen backlog
        'max_request'   => 50,
        'dispatch_mode' => 1,
    ));
    //监听连接进入事件
    $serv->on('Connect', function ($serv, $fd, $reactor_id) {  
        echo "Client: Connect.\n";
    });

    //监听数据接收事件
    $serv->on('Receive', function ($serv, $fd, $from_id, $data) {
        $serv->send($fd, "Server: ".$data);
    });

    //监听连接关闭事件
    $serv->on('Close', function ($serv, $fd) {
        echo "Client: Close.\n";
    });

    //启动服务器
    $serv->start(); 
    ```


- 连接该端口

	```
    $ telnet 127.0.0.1 9501
  ```

