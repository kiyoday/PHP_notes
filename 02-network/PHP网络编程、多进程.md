## TCP协议介绍

#### 2.1 TCP协议的特点

- 面向连接
- 字节流协议
- 全双工
- 可靠的差错控制和流量控制

#### 2.2 TCP协议的创建

- 客户端主动调用 connect 发送` SYN `分节
- 服务器端必须回复一个 `ACK `分节来确认客户端 SYN 分节，并发送一个 `SYN` 分节到客户端
- 客户端对服务器端发送的 SYN 分节进行` ACK` 确认

![TCP三次握手示意图](https://doc.shiyanlou.com/document-uid582973labid4047timestamp1511320391154.png/wm)

至此，成功建立 TCP 连接，用于接下来的数据传输

#### 2.3 TCP协议的拆除

因为 TCP 为全双工的传输协议，所以拆除连接的时候，需要四次分节的交换

- 首先申请拆除的一端调用 close 发送一个` FIN` 分节
- 另一端接收到 `FIN `分节时，发送一个` ACK `分节进行确认
- 同理，另一端要申请拆除连接时，也要发送一个` FIN` 分节
- 接收端发送 `ACK `分节进行确认

至此，成功拆除 TCP 连接

![TCP四次握手示意图](https://doc.shiyanlou.com/document-uid582973labid4047timestamp1511321157227.png/wm)

上图展示了客户端主动发送关闭的流程，事实上服务器也是可以执行主动关闭的。

#### 2.4 TCP的状态转换图

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid582973labid4047timestamp1511321375410.png/wm)

- SYN_SENT 主动打开，SYN 分节已发送
- SYN_RCVD 被动打开，SYN 分节已接收
- ESTABLISHED 已经建立连接

- FIN_WAIT_1 发起主动关闭，FIN 分节已发送
- CLOSE_WAIT 被动关闭， FIN 分节已接收，ACK 分节已发送
- FIN_WAIT_2 成功实现半关闭，ACK 分节已接收
- LAST_ACK 最终的 ACK， FIN 分节已发送
- TIME_WAIT FIN 分节已接收， ACK 分节已发送
- CLOSED ACK 分节已接收，成功拆除连接

本章介绍了 TCP 协议的建立、拆除和状态转换，TCP 协议是 SOCKET 编程最常用的传输协议，是 HTTP、FTP 协议的基石，所以理解好 TCP 协议是非常有必要的。

使用`netstat`命令观察TCP的状态

## Socket编程

### 1 Socket简介

Socket 的官方解释： 在网络编程中最常用的方案便是`Client/Server(客户机/服务器)`模型。在这种方案中客户应用程序向服务器程序请求服务。一个服务程序通常在一个众所周知的地址监听对服务的请求，也就是说，服务进程一 直处于休眠状态，直到一个客户向这个服务的地址提出了连接请求。在这个时刻，服务程序被"惊醒"并且为客户提供服务－对客户的请求作出适当的反应。

为了方便这种Client/Server模型的网络编程，90年代初，由Microsoft联合了其他几家公司共同制定了一套WINDOWS下的网络编程接口，即WindowsSockets规范，它不是一种网络协议，而是一套`开放`的、`支持多种协议`的Windows下的`网络编程接口`。现在的Winsock已经基本上实现了与协议无关，你可以使用Winsock来调用多种协议的功能，但较常使用的是TCP/IP协议。Socket实际在计算机中提供了一个通信端口，可以通过这个端口与任何一个具有Socket接口的计算机通信。应用程序在网络上传输，接收的信息都通过这个Socket接口来实现

我们可以简单的把 Socket 理解为一个可以连通网络上不同计算机应用程序之间的管道，把一堆数据从管道的 A 端扔进去，则会从管道的 B 端（同时还可以从C、D、E、F……端冒出来）。

### 2 Socket 函数介绍

Socket 通信依次会进行 Socket 创建、Socket 监听、Socket 收发、Socket 关闭几个阶段，下面我们列举出 PHP 网络编程中最常用也是必不可少的几个常用的函数进行进一步的说明。

#### socket_create

TODO ： 创建一个新的 socket 资源 函数原型: `resource socket_create ( int $domain , int $type , int $protocol )` 它包含三个参数，分别如下：

- domain：AF_INET、AF_INET6、AF_UNIX，`AF`的释义就 `address family`，地址族的意思，我们常用的有 ipv4、ipv6
- type: SOCK_STREAM、SOCK_DGRAM等，最常用的就是`SOCK_STREAM`，基于字节流的SOCKET类型，也是TCP协议使用的类型
- protocol: SOL_TCP、SOL_UDP 这个就是具体使用的传输协议，一般可靠的传输我们选择 TCP，游戏数据传输我们一般选用 UDP 协议

#### socket_bind

TODO ： 将创建的 socket 资源绑定到具体的 ip 地址和端口 函数原型: `bool socket_bind ( resource $socket , string $address [, int $port = 0 ] )`

它包含三个参数，分别如下：

- socket: 使用`socket_create`创建的 socket 资源，可以认为是 socket 对应的 id
- address: ip 地址
- port: 监听的端口号，WEB 服务器默认80端口

#### socket_listen

TODO ： 在具体的地址下监听 socket 资源的收发操作 函数原型: `bool socket_listen ( resource $socket [, int $backlog = 0 ] )`

它包含两个个参数，分别如下：

- socket: 使用`socket_create`创建的socket资源
- backlog: 等待处理连接队列的最大长度

#### socket_accept

TODO ： 接收一个新的 socket 资源 函数原型: `resource socket_accept ( resource $socket )`

- socket: 使用`socket_create`创建的socket资源

#### socket_write

TODO ： 将指定的数据发送到 对应的 socket 管道 函数原型: `int socket_write ( resource $socket , string $buffer [, int $length ] )`

- socket: 使用`socket_create`创建的socket资源
- buffer: 写入到`socket`资源中的数据
- length: 控制写入到`socket`资源中的`buffer`的长度，如果长度大于`buffer`的容量，则取`buffer`的容量

#### socket_read

TODO ： 获取传送的数据 函数原型: `int socket_read ( resource $socket , int $length )`

- socket: 使用`socket_create`创建的socket资源
- length: `socket`资源中的`buffer`的长度

#### socket_close

TODO ： 关闭 socket 资源 函数原型: `void socket_close ( resource $socket )`

- socket: `socket_accept`或者`socket_create`产生的资源，不能用于`stream`资源的关闭

#### stream_socket_server

由于创建一个SOCKET的流程总是 socket、bind、listen，所以PHP提供了一个非常方便的函数一次性创建、绑定端口、监听端口

函数原型: `resource stream_socket_server ( string $local_socket [, int &$errno [, string &$errstr [, int $flags = STREAM_SERVER_BIND | STREAM_SERVER_LISTEN [, resource $context ]]]] )`

- local_socket: 协议名://地址:端口号
- errno: 错误码
- errstr: 错误信息
- flags: 只使用该函数的部分功能
- context: 使用`stream_context_create`函数创建的资源流上下文

### 3 socket文件编辑

基于上面的函数我们可以很方便的去构建一个 socket 通信程序，我们在`/home/shiyanlou/`目录下去编辑一个 `server.php`，如下：

```php
<?php 

$sock = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);

socket_bind($sock, "127.0.0.1", 8080);

socket_listen($sock);

for ( ; ; ) {
    $conn = socket_accept($sock);

    $write_buffer = "HTTP/1.0 200 OK\r\nServer: my_server\r\nContent-Type: text/html; charset=utf-8\r\n\r\nhello!world";

    socket_write($conn, $write_buffer);

    socket_close($conn);
}
```

运行:

```shell
sudo service php7.2-fpm start;
php server.php
```

运行成功后，打开浏览器，输入`http://127.0.0.1:8080`，回车看到结果`hello!world`，同时，我们也可以使用集成函数`stream_socket_server`去改写成：

```php
<?php 

$sock = stream_socket_server("tcp://127.0.0.1:8080", $errno, $errstr);

for ( ; ; ) {
    $conn = stream_socket_accept($sock);

    $write_buffer = "HTTP/1.0 200 OK\r\nServer: my_server\r\nContent-Type: text/html; charset=utf-8\r\n\r\nhello!world";

    fwrite($conn, $write_buffer);

    fclose($conn);
}
```

> 需要注意的是，这里不能使用`socket_accept`，因为`stream_socket_server`和`socket_create`创建的不是同一种资源，
>
> `stream_socket_server`创建的是`stream`资源，这也是为什么可以用`fwrite`、`fread`、`fclose`操作该资源的原因. 
>
> 而`socket_create`创建的是`socket`资源，而不是`stream`资源，所以`socket_create`创建的资源只能用`socket_write`、`socket_read`、`socket_close`来操作.

## 多进程编程

### 1 多进程简介与代码实现

简而言之，就是多个进程同时工作，这样的进程一般属于亲属关系，通常由一个父进程`fork`得到的. 注意这里所说的同时工作，是宏观上的，同一时刻在单个单核CPU上，还是只有一个进程在执行。

先上一个简单的示例,在`/home/shiyanlou/`目录下创建文件`multiProcess.php`:

```php
//multiProcess.php
<?php 

$pid = pcntl_fork();

if ($pid) {
    //父进程
    echo "This is parent process\n";
    pcntl_waitpid($pid, $status);
} elseif ($pid == 0) {
    //子进程
    echo "This is child process\n";
} else {
    die("fork failed\n");
}
php multiProcess.php
```

#### pcntl_fork

函数原型: `int pcntl_fork ( void )`

执行该函数，会复制当前进程产生另一个进程，称之为当前进程的子进程，该函数在父进程和子进程的返回值不相同，在父进程中返回的是`fork`出的子进程的进程ID，而在子进程中返回值为0。

要注意的是在复制进程时，会复制该进程的数据（堆数据、栈数据和静态数据），包括在父进程打开的文件描述符，在子进程中也是打开的，这意味着当你在父进程使用了大量内存时，`fork`出来的子进程必须拥有等量的内存资源，否则可能会导致`fork`失败.

还有的同学也许会注意到，父进程还调用了一个函数`pcntl_waitpid`，这个函数的作用是什么呢？

#### pcntl_waitpid

函数原型: `int pcntl_waitpid ( int $pid , int &$status [, int $options = 0 ] )`

- pid: 进程ID
- status: 子进程的退出状态
- option: 取决于操作系统是否提供`wait3`函数，如果提供该函数，则该选项参数才生效.

### 2 僵尸进程

为什么父进程要调用这个函数呢？这是因为子进程在结束时，不管是主动结束（调用exit或main函数返回）还是被动结束（被发出的信号打断），都会保存退出状态供父进程调用，所以还会在操作系统的进程表中占用一项。如果不调用`pcntl_waitpid`清除子进程的退出状态，回收该表项，那么子进程虽然已经死亡，但依然占用着宝贵的资源，就变成了“僵尸进程”。

我们稍微更改一下上面的代码，做个实验来观察“僵尸进程”,在`/home/shiyanlou/`目录下创建文件`zombieProcess.php`:

```php
//zombieProcess.php
<?php 

$pid = pcntl_fork();

if ($pid) {
    //父进程
    echo "This is parent process\n";
    sleep(30);
} elseif ($pid == 0) {
    //子进程
    echo "This is child process\n";
} else {
    die("fork failed\n");
}
```

开启两个终端，先让其中一个运行`top`命令, 再让另一个运行`php zombieProcess.php`. 就会观察到有一个僵尸进程产生。

![zombie进程的产生示意图](https://doc.shiyanlou.com/document-uid582973labid4049timestamp1511331386988.png/wm)

我们现在已经掌握了如何`fork`一个子进程，并如何避免僵尸进程的产生. 接下来，是时候为我们的WEB服务器赋能，让它能同时支持更多的并发

### 3 leader-follower模型

一个非常简单的leader-follower模型，创建一个进程池，随机选出一个进程作为`leader`进程，该进程监听是否有新连接，如果有则提升另一个`follower`为`leader`进程来继续监听，而原`leader`进程则去处理新连接的请求，在`/home/shiyanlou/`目录下创建文件`leader.php`:

```php
<?php 

$sock = stream_socket_server("tcp://127.0.0.1:80", $errno, $errstr);

$pids = [];

for ($i=0; $i<10; $i++) {

    $pid = pcntl_fork();
    $pids[] = $pid;

    if ($pid == 0) {
        for ( ; ; ) {
            $conn = stream_socket_accept($sock);

            $write_buffer = "HTTP/1.0 200 OK\r\nServer: my_server\r\nContent-Type: text/html; charset=utf-8\r\n\r\nhello!world";

            fwrite($conn, $write_buffer);

            fclose($conn);
        }

        exit(0);
    }

}

foreach ($pids as $pid) {
    pcntl_waitpid($pid, $status);
}
```

这样，我们的WEB服务器的处理能力又上了一个台阶，可以同时处理10个并发，当然这个能力还会随着你的进程池中进程的数量提升。那是不是意味着只要我们无限加大进程的数量，就可以处理无限的并发呢？遗憾的是，事实并不是这样。

首先，系统创建进程的开销是大的，系统并不能无限地创建进程，因为每一个进程都占用一定的系统资源，而系统的资源是有限的，不可能无限地创建。 其次，大量进程带来的上下文切换，也会带来巨大的资源消耗和性能浪费。

所以使用大量地创建进程的方式来提升并发，是不可行的。那么，没有办法了么？难道没有一种技术在单进程里就可以维持成千上万的连接么？下一章我们将介绍IO复用技术，使我们WEB服务器的并发处理量再次提升。

## I0复用

#### 2.1 阻塞／非阻塞

这两个概念是针对 IO 过程中进程的状态来说的，阻塞 IO 是指`调用结果`返回之前，**当前线程会被挂起**；

相反，非阻塞指在不能立刻得到结果之前，该函数**不会阻塞当前线程，而会立刻返回**。

#### 2.2 同步／异步

这两个概念是针对调用如果返回结果来说的，所谓同步，就是在发出一个功能调用时，在没有得到结果之前，该**调用就不返回**；

相反，当一个异步过程调用发出后，调用者不能立刻得到结果，实际处理这个调用的部件在完成后，通过`状态、通知和回调`来通知调用者。

#### 2.3 阻塞与非阻塞

在介绍IO复用技术之前，先介绍一下阻塞和非阻塞，在我们前几节的WEB服务器中，调用`socket_accept`函数会使整个进程阻塞，直到有新连接，操作系统才唤醒进程继续执行。而非阻塞模式, `stream_socket_accept`的行为就不一样了，如果没有新连接，不会阻塞进程，而是马上返回false。

#### 2.4 IO 多路复用

多路复用（IO/Multiplexing）：为了提高数据信息在网络通信线路中传输的效率，在一条物理通信线路上建立多条逻辑通信信道，同时传输若干路信号的技术就叫做多路复用技术。对于 Socket 来说，应该说能同时处理多个连接的模型都应该被称为多路复用，目前比较常用的有 select/poll/epoll/kqueue 这些 IO 模型（目前也有像 Apache 这种每个连接用单独的进程/线程来处理的 IO 模型，但是效率相对比较差，也很容易出问题，所以暂时不做介绍了）。在这些多路复用的模式中，异步阻塞/非阻塞模式的扩展性和性能最好。

#### 2.5 select 轮询

使用`select`会轮询连接池，当有连接可读或可写时，`select`函数返回可读写的连接数，然后再轮询一遍连接池，查找活动连接进行读写操作。

比较尴尬的是，`socket_select`只支持`socket`类型的资源，而不支持`stream`类型的资源，所以这里需要使用`socket_create`创建`socket`资源，在`/home/shiyanlou/`目录下创建文件`select.php`：

```php
<?php
$sock = socket_create(AF_INET, SOCK_STREAM, 0);

socket_bind($sock, "127.0.0.1", 80);

socket_listen($sock);

$reads = [];
$clients = [];
$writes = NULL;
$exceptions = NULL;

socket_set_nonblock($sock);

$write_buffer = "HTTP/1.0 200 OK\r\nServer: my_server\r\nContent-Type: text/html; charset=utf-8\r\n\r\nhello!world";

for ( ; ; ) {

    $reads = array_merge(array($sock), $clients);    

    $activity_counts = @socket_select($reads, $writes, $exceptions, 0);

    if ($activity_counts > 0) {

        if (($conn = socket_accept($sock)) !== false) {
            $clients[] = $conn;
        }


        $length = count($clients);
        for ($i = 0; $i < $length; $i++) {
            $client = $clients[$i];

            if (($read_buffer = @socket_read($client, 1024)) != false) {
                socket_write($client, $write_buffer);
                socket_close($client);
                break;
            }
        }

    }
}
```

`select`虽然可以监听多个连接，但是它最多只能监听1024个连接。这虽然在`poll`中得到了改进，但是`select`和`poll`本质上都是通过轮询的方式进行监听，这意味着当监听了上万连接时，就算只有一个连接是活动的，依然要把上万连接都遍历一次。显然，这无疑是极大的性能浪费，而`epoll`的出现彻底地解决了这个问题

#### 2.6 epoll

`epoll`并不是只有一个函数来实现，而是多个函数。我们这里并不讨论`epoll`相关的函数，因为PHP并不提供相关的函数，但它提供了基于`libevent`库的`libevent`扩展，以及基于`libevent`库的`event`扩展。`libevent`库实现了`Reactor`模型，关于`Reactor`模型，这里只作简单的介绍

`Reactor`模型，包含了几个组件：句柄，事件分发器，事件处理器。

- 句柄，就是文件描述符，在`Socket`编程中，就是使用`socket_create`创建的`socket`资源.
- 事件分发器, 通过事件循环，事件循环是通过诸如`epoll``Select``Poll`等IO复用技术实现的，监听句柄期待的事件是否发生，发生了则将事件分发给事件处理器.
- 事件处理器，当事件发生时，处理相关的逻辑.

而`libevent`库已经实现了`Reactor`模型，我们可以开箱即用。下面，我们将通过`libevent`对我们的WEB服务器再次改造，使它的处理并发的能力再次提高

在此之前，我们需要安装`event`扩展，安装`event`扩展必须安装`libevent`库

```shell
sudo apt-get update
sudo apt-get install php-dev
$ cd
$ wget https://github.com/downloads/libevent/libevent/libevent-2.0.20-stable.tar.gz
```

`注意：`若遇到链接无法访问的情况，请尝试使用这个地址：`http://labfile.oss.aliyuncs.com/courses/987/libevent-2.0.20-stable.tar.gz` 解压并编译

```shell
tar -xvf libevent-2.0.20-stable.tar.gz
cd libevent-2.0.20-stable
./configure
make 
sudo make install
```

在通过pecl方式安装event的过程中，在询问要不要支持openssl时，输入no，其它都是默认直接回车

```shell
sudo pecl install event
```

安装完之后， `sudo vim /etc/php5/cli/conf.d/20-event.ini`

> 在php7.2中路径为`sudo vim /etc/php/7.2/cli/conf.d/20-event.ini`

添加扩展`extension=event.so`，保存退出，运行以下代码进行验证是否安装成功

```shell
php -m | grep event
```

在`/home/shiyanlou/`目录下创建文件`epoll.php`：

```php
<?php

$fd = stream_socket_server("tcp://127.0.0.1:80", $errno, $errstr);

stream_set_blocking($fd, 0);

$event_base = new EventBase();

$event = new Event($event_base, $fd, Event::READ | Event::PERSIST, function ($fd) use (&$event_base) {
 $conn = stream_socket_accept($fd);

 fwrite($conn, "HTTP/1.0 200 OK\r\nContent-Length: 2\r\n\r\nHi");
 fclose($conn);
}, $fd);

$event->add();

$event_base->loop();    
```

流程和创建`Reactor`模型一致

- 创建句柄
- 创建事件循环器
- 创建事件，并指定事件监听的事件类型及注册事件处理器
- 向循环器中添加事件

这里我们主要看`Event`类，看看它的构造函数原型:

```php
public Event::__construct ( EventBase $base , mixed $fd , int $what , callable $cb [, mixed $arg = NULL ] )
```

- base: `EventBase`类的实例
- fd: 要监听的句柄
- what: 要监听的事件类型
- cb: 事件处理器，在PHP中就是回调函数
- arg: 事件处理器的参数列表

通过我们进一步的改造，我们的WEB服务器现在处理并发的能力已经非常强劲，但是要用于生产环境，还有一些需要解决的问题，下一章我们将探讨如何让WEB服务器进程脱离控制终端，变为守护进程

## 进程间通信及守护进程

前几章中，我们写了一个WEB服务器，并在shell终端中使用`sudo php server.php`成功地运行。但我们如何做到关闭终端之后，WEB服务器继续在后台运行呢？答案是通过`守护进程`，本节实验就将为大家介绍什么是守护进程，如何将一个普通的进程转换为守护进程，如何通过新号去管理守护进程。

### 1.1 实验知识点

- 守护进程
- 信号
- 创建守护进程
- 模拟信号传递管理进程

### 1 守护进程



#### 进程的几个ID

- pid 进程ID
- ppid 父进程ID
- pgid 进程组ID
- sid 会话ID

```shell
ps -axj
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid582973labid4057timestamp1511421100719.png/wm)

一般PPID为0的，都是内核态进程。一般PPID为1的，并且`pid == pgid == sid`的，都是守护进程

#### 守护进程创建的标准流程

让WEB服务器进程变为守护进程，成为守护进程有几个标准的步骤：

- 设置文件创建掩码，一般设置为0，`umask(0)`
- `pcntl_fork`一个子进程，并马上退出，这样做的目的是让子进程继承进程组ID并获取一个新的进程ID，这样就可以确保子进程一定不是进程组组长，因为进程组组长不能创建新会话
- `posix_setsid`创建新会话和新进程组，并成为会话组长和进程组组长，并和原来的控制终端脱离关系，这样该进程就不会被原来终端的控制信号中断
- `pcntl_fork`，再`fork`一次并不是必须的，只是在基于`System-V`的系统上，有人建议再`fork`一次，避免打开终端设备，使程序的通用性更强。我们在 `/home/shiyanlou/`目录下创建文件`daemon.php`:

```php
<?php 
//daemon.php
function daemon() {
    umask(0);

    if (pcntl_fork()) {
        exit(0);
    }

    posix_setsid();

    if (pcntl_fork()) {
        exit(0);
    }

    sleep(100);
}

daemon();
```

然后在终端中运行命令：

```shell
php daemon.php
ps axj | grep daemon.php
```

观察一下`ppid`、`pid`、`pgid`、`sid`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid582973labid4057timestamp1511421840272.png/wm)

`ppid`确实为1，这证明进程已经被`init`1号进程收养。但是为什么`pid`、`pgid`、`sid`这三个值不一样呢？是不是弄错了？我们再看看代码，在调用`posix_setsid`之后，这三个值其实是一样的，只是我们又`fork`了一次，所以`pid`变了。有兴趣的同学把第二次`fork`的代码注释点，再观察一下，是不是一样了？

现在，我们又要对我们上一节的WEB服务器进行改进，让它变为守护进程，我们将之前的`server.php`改写成如下：

```php
//server.php
<?php

    function daemon() {
    umask(0);

    if (pcntl_fork()) {
        exit(0);
    }

    posix_setsid();

    if (pcntl_fork()) {
        exit(0);
    }
}

daemon();

$fd = stream_socket_server("tcp://127.0.0.1:8080", $errno, $errstr);

stream_set_blocking($fd, 0);

$event_base = new EventBase();

$event = new Event($event_base, $fd, Event::READ | Event::PERSIST, function ($fd) use (&$event_base) {
    $conn = stream_socket_accept($fd);

    fwrite($conn, "HTTP/1.0 200 OK\r\nContent-Length: 2\r\n\r\nHi");
    fclose($conn);
}, $fd);

$event->add();

$event_base->loop(); 
sudo php server.php
```

运行成功之后，关闭当前终端，打开另一终端，输入 `ps axj | grep server.php`观察`pid`、`pgid`、`sid`、`ppid`，并打开浏览器输入`127.0.0.1:8080`，看是否输出结果

到这儿，我们的WEB服务器才相对完善一些了，那有的同学就又要问了，变成了守护进程，那我要怎么控制它重启，暂停呢？接下来的一节我们将介绍如何使用信号与守护进程进行通信。

### 2 信号



我们在使用控制终端的时候，在上面键入各种各样的子程序，比如`sudo apt-get`安装程序，但有的时候子程序运行时间过长，我们没有耐心等下去时，我们经常会按`Ctrl+c`结束当前进程的运行，`Ctrl+c`实质上就是发送一个`SIGINT`信号给子程序，子程序的信号处理器接收到该信号之后，就会按预先编好的程序进行处理，这样的话即使我们脱离终端，无法进行直接的手动操作也可以利用信号控制我们编写程序的状态，那在PHP中我们如何调用函数发送信号呢？

#### 2.2.1 posix_kill

函数原型: `bool posix_kill ( int $pid , int $sig )`

- pid: 进程ID
- sig: 系统预定义的信号常量

#### 2.2.2 pcntl_signal

函数原型: `bool pcntl_signal ( int $signo , callback $handler [, bool $restart_syscalls = true ] )`

- signo: 系统预定义的信号常量
- handler: 信号处理器，一个回调函数
- restart_syscalls: 当进程在进行系统调用时，被信号中断时，系统调用是否重新调用，一般默认为true

接下来我们利用上述函数来模拟一个进程间的信号传递达到控制程序的效果，在 `/home/shiyanlou` 目录下编辑文件`signal.php`：

```php
//signal.php
<?php

declare(ticks=1);

pcntl_signal(SIGINT, function() {
   file_put_contents("signal.txt", "signal recevied\n"); 
});

sleep(30);
```

编辑完成之后我们在命令行中执行：

```shell
$ php signal.php
```

在进程返回结果之前，我们按下`Ctrl+c`，此时系统会自动调用`kill`发送信号 SIGINT 我们编写的信号处理器进行信号的处理执行回调函数。除了使用`pcntl_signal`安装信号处理器，我们在上一章说过的`Event`类，也可以监听信号事件，将`signal.php`改写为：

```php
//signal.php
<?php
$event_base = new EventBase();

$event = new Event($event_base, SIGINT, Event::SIGNAL, function () use (&$event_base) {
    file_put_contents("signal2.txt", "signal recevied\n");
});

$event->add();

$event_base->loop();
```

## 综合练习

本节实验我们将结合之前的实验内容实现一个比较完整的 web 服务器

我们将使用面向对象对`event.php`进行重构，如下：

```php
//event.php
<?php

class MyEvent
{
    public $event_base;

    public $events = [];

    public function __construct()
    {
        $this->event_base = new EventBase();
    }

    public function add($fd, $what, $callback, $callback_arg)
    {
        $event = new Event($this->event_base, $fd, $what, $callback, $callback_arg);

        $this->events[intval($fd)] = $event;

        $event->add();
    }

    public function remove($fd)
    {
        $event = $this->events[intval($fd)];
        $event->free();
    }

    public function loop()
    {
        $this->event_base->loop();
    }
}
```

使用面向对象对`server.php`进行重构，如下：

```php
//server.php
<?php

require './event.php';

class Server
{
    public $ip = '';

    public $port = 8080;

    public $path = "./pid.txt";

    public $event;

    public static function daemon()
    {
        umask(0); 

        $pid = pcntl_fork();

        if ($pid) {
            exit(0); 
        } elseif ($pid < 0) {
            die("fork failed\n");
        }

        $sid = posix_setsid(); 

        $pid = pcntl_fork();

        if ($pid) {
            exit(0); 
        } elseif ($pid < 0) {
            die("fork failed\n");
        }

        if ($sid < 0) {
            die("create session failed\n");
        }
    }

    public function __construct($ip, $port = 80)
    {
        $this->ip = $ip;
        $this->port = $port;
        $this->event = new MyEvent();
    }

    public function run()
    {
        if ($GLOBALS['argc'] > 1) {
            $this->sendSignal();
            exit(0);
        } else {
            self::daemon();
        }

        $this->installSignalHandler();
        $this->recordPid();
        $this->start();
    }

    public function sendSignal()
    {
        //检查服务器是否存活
        if (posix_kill($this->getPid(), 0)) {
            if (strpos($GLOBALS['argv'][1], "stop") !== false) {
                posix_kill($this->getPid(), SIGUSR1);
            }
        }
    }

    public function start()
    {
        $domain = sprintf("tcp://%s:%d", $this->ip, $this->port);

        $fd = stream_socket_server($domain, $errno, $errstr);

        if (!$fd) {
            die("$errno $errstr\n");
        }

        stream_set_blocking($fd, 0);

        $this->event->add($fd, Event::READ | Event::PERSIST, [$this, 'requestHandler'], $fd);

        $this->event->loop();
    }

    public function requestHandler($fd)
    {
        $conn = stream_socket_accept($fd);

        fwrite($conn, "HTTP/1.0 200 OK\r\nContent-Length: 2\r\n\r\nHi");
        fclose($conn);
    }

    public function installSignalHandler()
    {
        $this->event->add(SIGUSR1, Event::SIGNAL, [$this, "handler"], SIGUSR1);
    }

    public function handler($signo)
    {
        switch($signo) {
            default:
            case SIGUSR1:
                $this->event->remove($signo);
                $this->stop();
                break;
        }
    }

    public function stop()
    {
        exit(0);
    }

    public function getPid()
    {
        return file_get_contents($this->path);
    }

    private function recordPid()
    {
        file_put_contents($this->path, posix_getpid());
    }
}

$server = new Server("127.0.0.1");

$server->run();
```

本节实验我们利用面向对象结合了 Event 模型、信号、守护进程实现了一个比较完整的服务器端网络通信示例，供大家参考。至此，我们的教程就结束了，感谢学习。