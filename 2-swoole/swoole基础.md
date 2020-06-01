# 一、Swoole基础

## 简介

### 1. Swoole是什么

- PHP异步网络通信引擎
- 最终编译为<kbd>so文件</kbd>作为PHP的扩展
  
### 2. [特性](https://www.swoole.com/)
 - Swoole使用纯C语言编写，提供了PHP语言的<font color =red>**异步多线程**</font>服务器，**异步TCP/UDP网络**客户端，异步MySQL，异步Redis，数据库连接池，AsyncTask，消息队列，毫秒定时器，异步文件读写，异步DNS查询。Swoole内置 了Http/WebSocket服务器端/客户端、Http2.0服务器端。

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
   sudo apt install gcc -y &&
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
   sudo apt install libffi-dev -y
   ```
   
   
   
   ```bash
   $ ./configure --help #查看命令
   $ ./configure --prefix=/home/kiyo/study/soft/php #指定安装路径
   ```
   
3. **make **编译构建

4. **make install** 安装

   ```shell
   $ make -j && make install
   ```

5. 配置PHP环境PATH

   ```shell
   $ vi ~/.bash_profile
   
   #修改以下路径
   #export PATH
   #alias php=/home/Work/soft/php/bin/php
   
   $ source ~/.bash_profile
   ```

   