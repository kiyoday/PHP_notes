## MySQL版本类问题

## 用户管理类问题

### 一、给定场景下为某用户授权

#### 1.如何定义MySQL数据库账号?

- 用户名@可访问控制列表

    | 格式        | 意义                        |
    | ----------- | --------------------------- |
    | %           | 代表可以从所有外部主机访问  |
    | 192.168.1.% | 表示可以从192.168.1网段访问 |
    | localhost   | DB服务器本地访问            |

    

- 使用CREATE USER命令建立用户

#### 2.MySQL常用的用户权限

​	<img src="https://i.loli.net/2020/06/05/gZnUi9EhkJISKrq.png" alt="image-20200605145348399" style="zoom:50%;" />

- 命令行中 `>show preivileg`

<img src="https://i.loli.net/2020/06/05/sphjizLbeX4uQU2.png" alt="image-20200605145536499" style="zoom:50%;" />

#### 3.如何为用户授权?

- 使用grant命令

  ```mysql
  grant select,insert,update,delete on db.tb to user@ip;	#授权
  revoke delete on db.tb from user@ip;	#收回权限
  ```



---

### 二、保证数据库账号的安全

最小权限原则

密码强度策略

密码过期原则

限制历史密码重用原则







## 服务器配置类问题

## 在日志类问题

## 存储引擎类问题

## MySQL架构类问题

## 备份恢复类问题

## 管理及监控类问题