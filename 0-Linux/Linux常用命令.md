# Linux常用命令

## 常用命令

| 模块         | 命令                                         |
| ------------ | -------------------------------------------- |
| 系统安全     | sudo、su、 chmod、setfac                     |
| 进程管理     | w、top、ps、kill、 pkill,、 pstree、 killall |
| 用户管理     | id、 usermod、 useradd、 groupadd、 userdel  |
| 文件系统     | mount、 umount、fsck、df、du                 |
| 网络应用     | curl、 telnet、mail、 elinks                 |
| 网络测试     | ping、 netstat、host                         |
| 网络配置     | hostname、 ifconfig                          |
| 软件包管理   | yum、rpm、apt-get                            |
| 文件内容查看 | head、tail、less、more                       |
| 文件处理     | touch、 unlink、 rename、ln、cat             |
| 目录操作     | cd、mv、rm、pwd、tree、cp、ls                |
| 文件权限属性 | setfac、 chmod、 chown、 chgrp               |
| 文件传输     | ftp、scp                                     |

## 定时任务

- crontab命令

```shell
crontab -e
* * * * * 命令(分时日月周)
```

- at命令 一次性执行命令

```shell
$ at 2: 00 tomorrow
at>/home/Jason/do job
at> Ctrl + D #退出
```

## vi/vim编辑器

- 模式

  | 模式       | 说明/命令              |
| ---------- | ---------------------- |
  | 一般模式   | 删除、复制和粘贴       |
| 编辑模式   | i、I、o、O、a、A、r、R |
    | 命令行模式 | :      /       ?       |
    
- 操作

    | 操作             | 指令                                                         |
    | ---------------- | ------------------------------------------------------------ |
    | 移动光标         | ctrI+f、ctrl+b、 0或者功能键Home、$或者功能键End、G、gg、N + enter |
    | 查找             | /word、?word                                                 |
    | 替换             | :n1,n2  s/word1/word2/g、:1,$ s/word1/word2/g、:1$ s/word1/word2/gc |
    | 删除、复制和粘贴 | xX、dd、ndd、 yy、nyy. p、P、ctrl+r.                         |
    | 保存和退出       | w、q、wq、q!                                                 |
    | 视图模式（vim)   | v、V、ctrl+v、y、d                                           |
    | 配置             | :setnu、 :setnonu                                            |

    

## shell

- 脚本执行方式

| 方式                   | 命令                             |
| ---------------------- | -------------------------------- |
| 赋予权限,直接执行      | chmod +x test.sh; ./test.sh      |
| 调用解释器使得脚本执行 | bash、csh、 csh、 ash、 bsh、ksh |
| 使用source命令         | source test.sh                   |



- 编写基础

  开头用#!指定脚本解释器
  `#!/bin/sh`