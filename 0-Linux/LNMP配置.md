# LNMP环境配置

### 1. 环境

- linux环境下开发
- <font color=red>php7    swoole2.1    redis</font>
- <font color=red>源码安装php7   源码安装swoole</font>

### 2. PHP7源码编译安装

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




### 3. 安装Swoole

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

### 4. nginx 源码安装

[nginx官网](https://nginx.org/en/download.html)下载安装包解压

[安装方法](https://www.php.net/manual/zh/install.unix.nginx.php)

$ ./configure --prefix=/home/kiyo/study/soft/nginx #指定安装路径

### 5. yum版安装

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

  

### 6.MYSQL安装

- 下载

  [下载地址](https://dev.mysql.com/downloads/mysql/)

  选择`source code` == **Generic Linux**

  使用wget命令

- 解压

```shell
$ tar -zxvf #解压
```

- 安装依赖环境

  ```shell
  $ sudo yum install cmake gcc-c++ ncurses-devel perl-Data-Dumper boost boost-doc boost-devel
  ```

- 进入目录 cmake编译

  ```shell
  $ cmake \
  -DCMAKE_INSTALL_PREFIX=/home/soft/mysql \
  -DMYSQL_DATADIR=/data/mysql/data \
  -DSYSCONFDIR=/etc \
  -DMYSQL_USER=mysql \
  -DWITH_MYISAM_STORAGE_ENGINE=1 \
  -DWITH_INNOBASE_STORAGE_ENGINE=1 \
  -DWITH_ARCHIVE_STORAGE_ENGINE=1 \
  -DWITH_MEMORY_STORAGE_ENGINE=1 \
  -DWITH_READLINE=1 \
  -DMYSQL_UNIX_ADDR=/var/run/mysql/mysql.sock \
  -DMYSQL_TCP_PORT=3306 \
  -DENABLED_LOCAL_INFILE=1 \
  -DENABLE_DOWNLOADS=1 \
  -DWITH_PARTITION_STORAGE_ENGINE=1 \
  -DEXTRA_CHARSETS=all \
  -DDEFAULT_CHARSET=utf8 \
  -DDEFAULT_COLLATION=utf8_general_ci \
  -DWITH_DEBUG=0 \
  -DMYSQL_MAINTAINER_MODE=0 \
  -DWITH_SSL:STRING=bundled \
  -DWITH_ZLIB:STRING=bundled \
  -DFORCE_INSOURCE_BUILD=1
  ```

- yum安装

  ```
  yum list mysql*
  ```

  使用root登录

  1.确保服务器系统处于最新状态

  [root@localhost ~]# yum -y update

  如果显示以下内容说明已经更新完成

  Replaced:

   grub2.x86_64 1:2.02-0.64.el7.centos  grub2-tools.x86_64 1:2.02-0.64.el7.centos

  Complete!

  2.重启服务器

  [root@localhost ~]# reboot

  3.首先检查是否已经安装，如果已经安装先删除以前版本，以免安装不成功

  [root@localhost ~]# php -v

  或

  [root@localhost ~]# rpm -qa | gerp mysql

  或

  [root@localhost ~]# yum list installed | grep mysql

  如果显示以下内容说明没有安装服务

  -bash: gerp: command not found

  4.下载MySql安装包

  [root@localhost ~]# rpm -ivh http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm

  或

  [root@localhost ~]# rpm -ivh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

  5.安装MySql

  [root@localhost ~]# yum install -y mysql-server

  或

  [root@localhost ~]# yum install mysql-community-server

  如果显示以下内容说明安装成功

  Complete!

  6.设置开机启动Mysql

  [root@localhost ~]# systemctl enable mysqld.service

  7.检查是否已经安装了开机自动启动

  [root@localhost ~]# systemctl list-unit-files | grep mysqld

  如果显示以下内容说明已经完成自动启动安装

  mysqld.service                 enabled

  8.设置开启服务

  [root@localhost ~]# systemctl start mysqld.service

  9.查看MySql默认密码

  [root@localhost ~]# grep 'temporary password' /var/log/mysqld.log

  10.登陆MySql，输入用户名和密码

  [root@localhost ~]# mysql -uroot -p

  11.修改当前用户密码

  mysql>SET PASSWORD = PASSWORD('123456');

   update user set password=password('123456') where user='root' and host='localhost';

  set password for root@localhost = password('123456');

  12.开启远程登录，授权root远程登录

  mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'a123456!' WITH GRANT OPTION;

  13.命令立即执行生效

  mysql>flush privileges;

  \---------------------------------------------------------------------

  其他功能:

  \# 检查并且显示Apache相关安装包

  [root@localhost ~]# rpm -qa | grep mysql

  \# 删除MySql

  [root@localhost ~]# yum remove -y mysql mysql mysql-server mysql-libs compat-mysql51

  或

  [root@localhost ~]# rpm -e mysql-community-libs-5.7.20-1.el7.x86_64 --nodeps

  或

  [root@localhost ~]# yum -y remove mysql-community-libs-5.7.20-1.el7.x86_64

  \# 查看MySql相关文件

  [root@localhost ~]# find / -name mysql

  \# 重启MySql服务

  [root@localhost ~]# service mysqld restart

  \# 查看MySql版本

  [root@localhost ~]# yum repolist all | grep mysql

  \# 查看当前的启动的 MySQL 版本

  [root@localhost ~]# yum repolist enabled | grep mysql

  \# 通过Yum来安装MySQL,会自动处理MySQL与其他组件的依赖关系

  [root@localhost ~]# yum install mysql-community-server 

  \# 查看MySQL安装目录

  [root@localhost ~]# whereis mysql

  \# 启动MySQL服务

  [root@localhost ~]# systemctl start mysqld

  \# 查看MySQL服务状态

  [root@localhost ~]# systemctl status mysqld

  \# 关闭MySQL服务

  [root@localhost ~]# systemctl stop mysqld

  \# 测试MySQL是否安装成功

  [root@localhost ~]# mysql

  \# 查看MySql默认密码

  [root@localhost ~]# grep 'temporary password' /var/log/mysqld.log

  \# 查看所有数据库

  mysql>show databases;

  \# 退出登录数据库

  mysql>exit;

  \# 查看所有数据库用户

  mysql>SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;