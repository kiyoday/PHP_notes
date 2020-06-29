## 运行模式

php有着5种运行模式，常见的有4种:

### cgi 协议模式

cgi模式，通用网关接口（Common Gateway Interface），它允许web服务器通过特定的协议与应用程序通信， 调用原理大概为:

![cgi](C:\Users\12605\Desktop\PHP_notes\.img\cgi-1593421601922.svg)



> 用户请求→Web服务器接收请求→fork子进程 
>
> 调用程序/执行程序→程序返回内容/程序调用结束→web服务器接收内容→返回给用户 

由于每次用户请求，都得fork`创建`进程调用一次程序，然后`销毁`进程，所以`性能较低`

### fast-cgi 协议模式

fast-cgi是cgi模式的升级版，它像是一个`常驻型的cgi`，只要开启后，就可一直处理请求，不再需要`结束进程`， 调用原理大概为:![fast-cgi](C:\Users\12605\Desktop\PHP_notes\.img\fast-cgi.svg)
web服务器fast-cgi进程管理器初始化->预先fork n个进程
用户请求->web服务器接收请求->交给fast-cgi进程管理器（FPM）->fast-cgi进程管理区接收，给其中一个空闲fast-cgi进程处理->处理完成，fast-cgi进程变为空闲状态，等待下次请求->web服务器接收内容->返回给用户

> 注意，fast-cgi和cgi都是一种协议，开启的进程是单独实现该协议的进程

### 模块模式

apache+php运行时，默认使用的是模块模式，它把php作为apache的模块随apache启动而启动，接收到用户请求时则直接通过调用mod_php模块进行处理，详细内容可自行百度

### php-cli模式

php-cli模式属于命令行模式，对于很多刚开始学php就开始wamp，wnmp的开发者来说是最陌生的一种运行模式
该模式不需要借助其他程序，直接输入php xx.php 就能执行php代码
命令行模式和常规web模式明显不一样的是:

- 没有超时时间
- 默认关闭buffer缓冲
- STDIN和STDOUT标准输入/输出/错误 的使用
- echo var_dump，phpinfo等输出直接输出到控制台
- 可使用的类/函数 不同
- php.ini配置的不同

> 想要了解详细内容可查看http://php.net/manual/zh/features.commandline.php

### 其他

> 本文将以上除了php-cli的模式，都定义为常规web访问模式

## php-fpm

`PHP-FPM`（FastCGI 进程管理器）用于替换 PHP FastCGI 的大部分附加功能，对于高负载网站是非常有用的。
它的功能包括:

- 支持平滑`停止/启动`的高级进程管理功能;
- 可以工作于不同的 uid/gid/chroot 环境下，并监听不同的端口和使用不同的 php.ini 配置文件（可取代 safe_mode 的设置）;
- stdout 和 stderr 日志记录;
- 在发生意外情况的时候能够重新启动并缓存被破坏的 `opcode`;
- `文件上传`优化支持;
- "`慢日志`" - 记录脚本（不仅记录文件名，还记录 PHP backtrace 信息，可以使用 ptrace或者类似工具读取和分析远程进程的运行数据）运行所导致的异常缓慢;
- fastcgi_finish_request() - 特殊功能：用于在请求完成和刷新数据后，继续在后台执行耗时的工作（录入视频转换、统计处理等）;
- 动态／静态子进程产生;
- 基本 SAPI 运行状态信息（类似Apache的 mod_status）;
- 基于 php.ini 的配置文件。

### 工作原理:

它的工作原理大概为:
php-fpm启动->生成n个fast-cgi协议处理子进程->监听一个端口等待任务
用户请求->web服务器接收请求->请求转发给php-fpm->php-fpm交给一个空闲进程处理
->进程处理完成->php-fpm返回给web服务器->web服务器接收数据->返回给用户

> nginx+php-fpm 就是用的以上的方法

## php-cli

在前面的简单介绍中,我们已经了解了有php-cli这个模式,现在我们继续详细了解下php-cli和传统web模式不一样的地方吧

### 超时时间

在php-cli中,默认超时时间为永久不超时,但是可以通过`set_time_limit`设置超时时间.

```php
<?php
set_time_limit(1);
while (1){
}
```

### buffer缓冲

在常规web模式中,echo,var_dump,phpinfo等输出语句/函数,默认情况是先进入php缓冲区,等缓冲区到达一定数量,才开始传输给web服务器的,但是在php-cli模式中,默认关闭buffer,直接输出,例如以下代码:

```php
<?php
ob_start();//开启buffer缓冲区  php-cli下默认关闭buffer,由于web访问测试较麻烦,该段代码只为了查看以及测试缓冲区的作用,在web模式下,默认开启,无需手动开启,可自行配置
for($i=0;$i<1000;$i++){
    echo $i;
    sleep(1);
    if($i%10==0){
        //当i为10的倍数时,将直接结束并输出缓冲区的数据,然后再次开启缓冲区
        ob_end_flush();
        ob_start();
    }
}
```

> 也可通过ob_get_contents函数获取缓冲区内容,ob缓冲系列函数可自行搜索了解

buffer缓冲详细内容可查看:http://www.php20.cn/article/sw/buffer/104

### 标准输入/输出/错误

执行一个命令行都存在3个标准文件(linux一切皆文件):

- 标准输入 (stdin,通常对应终端的键盘,进程可通过该文件获取键盘输入的数据)

- 标准输出 (stdout,对应终端的屏幕,进程通过写入数据到该文件,将数据显示到屏幕)

- 标准错误 (stderr,对应终端的屏幕,进程通过写入数据到该文件,将错误信息显示到屏幕) 在php-cli命令行下,可通过以上3个文件句柄进行一系列的逻辑操作,比如:

  启动php文件,监听标准输入,获取到输入的网址,php再进行网址的数据请求/接收 等等操作 而在常规web模式下,标准输出会被拦截

  > echo var_dump等输出函数其实就是stdout,但是在常规web访问下被重定向到了web服务器,然后由web服务器输出

了解详细内容可查看http://www.php20.cn/article/156

### php-cli 专属扩展

php有些扩展在常规web下运行时没用/没有意义的 例如:

- swoole扩展
- socket扩展
- 等