## 消息队列的概念、原理和场景

### 1. 消息队列概念

1. 队列结构的中间件

2. 消息放入后，不需要立即处理

3. 由订阅者/消费者按顺序处理

### 2.核心结构

![image-20200615131619746](https://i.loli.net/2020/06/15/K8nBeCdPWlTEHrs.png)

### 3.应用场景

- 数据需要冗余

  数据持久化存储到队列中，保证每条数据都处理成功

- 系统解耦

  入队系统和出队系统没有直接关系，崩溃不会直接影响其他系统

- 流量削峰

  防止秒杀系统高并发情况下崩溃

- 异步通信

  异步场景可以使用消息队列

- 扩展性

  订单与退货系统，订阅消息队列可拓展业务

- 排序保证

  队列单进单出特性

### 4.队列介质

- Mysql：可靠性高、易实现，速度慢
- Redis：速度快，单条大消息包时效率低
- 消息系统：RabbitMQ专业性强、可靠，学习成本高

### 5.消息处理触发机制

- 死循环方式读取：易实现，故障时无法及时恢复
- 定时任务：压力均分，有处理量上限
- 守护进程：类似于PHP-FPM和PHP-CG，需要shell基础

## 解耦案例:队列处理订单系统和配送系统

### 1.架构设计

![image-20200615132846768](https://i.loli.net/2020/06/15/TPmZx21sNwkjAcn.png)

### 2.程序流程

![image-20200615132924454](https://i.loli.net/2020/06/15/wlA1M3Ru8IT6xQg.png)

`goods.sh 脚本`

```sh
#!/bin/bash

#!/bin/bashdate "+%G-%m-%d %H:%M:S"
cd /home/sunshine/
php goods.php
```

定时任务部署，crontab -e 中写定时任务，2>&1 把错误输出转化成标准输出

> 和 >> 都是输出重定向符号，标准输出默认是打印到控制台，如果要导入到文件，就需要使用>或>>，>会覆盖已有的文件内容，而>>会附加到已有内容之后
> < 和 << 是输入重定向符号，从文件中读取内容

1> 标准输出，指标准信息输出路径（默认输出方式）
2> 错误输出，指错误信息输出路径
2>&1 把错误输出导入（合并）到标准输出流中

```shell
crontab -e
*/1 * * * * /home/sunshine/good.sh >> home/sunshine/log.log 2>&1
```

监控日志文件，实时输出日志内容

```shell
tail -f log.log
```

订单处理程序 order.php

```PHP
<?php
include_once './include/db.php';
if (!empty($_GET['mobile'])) {
    //这里首先应该是订单中心的处理流程
    //......
    //把用户get过来的数据进行过滤
    $order_id = rand(10000, 99999);
    //订单信息
    $insert_data = array(
        'order_id ' => $order_id,
        'mobile' => $_GET['mobile'],
        'created_at' => date('Y-m-d H:i:s'),
        'status' => 0,
    );
    //存储数据
    $db = DB::getInstance();
    $res = $db->insert('order_queue', $insert_data);
    if ($res) {
        echo $insert_data['id'] . '保存成功';
    } else {
        echo $insert_data['id'] . '保存失败';
    }
}
```

商品处理程序 goods.php

```PHP
<?php
include './../include/db.php';
$db = DB::getInstance();
//1先把要处理的记录更新为等待处理
$wating = array('status' => 0);
$lock = array('status' => 2);
$res_lock= $db->update('order_queue', $lock, $waiting, 2);
//2我们要选择出刚刚咱们更新的这些数据结构,然后进行配送系统的处理
if ($res_lock) {
    $res = $db->selectAll('order_queue', $lock);
    //然后由配货系统进行配货处理
    //...
    //3把这些处理过的程序更新为已完成
    $success = array(
        'status' => 1,
        'updated' => date('Y-m-d H:i:s')
    );
    $res_last = $db->update('order_queue', $success, $lock);
    if ($res_last) {
        echo 'Success' . $res_last;
    } else {
        echo 'Fail' . $res_last;
    }
}else{
    echo 'ALL FINISHED';
}
```

## 流量削峰: Redis-List

- LPUSH/LPUSHX：将值插入到（/存在的）列表头部
- RPUSH/RPUSHX：将值插入到（/存在的）列表尾部
- LPOP：移出并获取列表的第一个元素
- RPOP：移出并获取列表的最后一个元素
- LTRIM：保留置顶区间内的元素
- **LLEN：获取列表长度**
- LSET：通过索引设置列表元素值
- LINDEX：通过索引获取列表中的元素
- LRANGE：获取列表置顶范围内的元素

### 1.架构设计

![image-20200617215859428](https://i.loli.net/2020/06/17/wsvSWrbGdDC1Ih5.png)

### 2.秒杀程序

1. 秒杀程序把请求写入Redis。(Uid , time_ stamp)
2. 检查Redis已存放数据的长度，超出，上限直接丢弃。
3. 死循环处理存入Redis的数据并入库

```php
</php
//首先,加载一个Reids组件
$redis = new Redis();
$resid->connect('127.0.0.1', 6379);
$reds_name = 'miaosha';
//接收用户的id
$uid = $_GET['uid'];
//获取redis里面已有的数量
$num = 10;
//如果当前人数少于10的时候，则加入这个队列 微秒时间戳
if ($redis->lLen($resid_name) < $num) {
  $redis->rPush($redis_name . $uid . '%' . microtime());
  echo $uid . '秒杀成功';
}else{
  //如果当前人数已经达到10人，返回秒杀已完成
  echo '秒杀已结束';
}
$redis->close();
```

结果入库程序

```php
<?php
inlcude_onceA './include/db.php';
$redis = new Redis();
$resid->connect('127.0.0.1', 6379);
$reds_name = 'miaosha';
$db = DB::getInstance();
//死循环
while(1) {
  //从队列最左侧取出一个值来
  $user = $redis->lPop($redis_name);
  //然后判断这个值是否存在
  if (!$user || $user == 'nil') {
    sleep(2);
    continue;
  }
  //切割出时间, uid
  $user_arr = explode('%', $user);
  $insert_data = array(
    'uid' => $user_arr[0],
    'time_stamp' => $use_arr[1]
  );
  //保存到数据库中
  $res = $db->insert('redis_queue', $insert_data);:
  //数据库插入失败时回滚
  if (!$res) {
    $redis->rPush($redis_name, $user);
  }
}
//释放redis
$redis->close();
```

### 问题与改进

上面的redis方案有问题，数量判断用获取len的做法不可取，同时两个人读取到是9，然后都rpush，此时队列中为11条数据。如果先存入队列，然后再lpop消耗，则可以避开数量判断这个并发性问题。

一般为防止`超卖`现象，解决并发问题，有以下两个解决思路：

解决思路1：

- 比如限购10个，提前先建一个包含10个数据的`计数队列`，然后有请求过来的时候用lpop排出该队列里的元素，并判断得到的值是否为空，如果排出正常数据，就将这个用户的id之类的信息`插入到另一个队列`，如果`排出为空`说明10个货物已被抢完，秒杀结束。因为pop是redis的原始操作，不用担心重复返回相同值的并发问题。

解决思路2：

- 在你的消费进程中设置为`单线程`处理，只处理10个记录。

## RabbitMQ

![image-20200617222550875](https://i.loli.net/2020/06/17/Jbq12imypdfTrA6.png)

### 1.安装

- RabbitMQ安装(rabbitmq-server，php-amqplib)

### 2.工作者模式

- `生产者`向`消息通道`发送消息
- `消费者`处理消息

