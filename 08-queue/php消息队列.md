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

##  消息队列（Message Queue）

“消息”是在两台计算机间传送的数据单位。消息可以非常简单，例如只包含文本字符串；也可以更复杂 ，包括对象等。

队列是一种数据结构，先进先出，保证了顺序性。

生产者：发送消息的一端。用于把消息写入到队列中

消费者：从消息队列中，依次读取每条消息的一端。

消息队列中间件是分布式系统中重要的组件，主要解决应用耦合，异步消息，流量削锋等问题。实现高性能，高可用，可伸缩和最终一致性架构。是大型分布式系统不可缺少的中间件。

目前在生产环境，使用较多的消息队列有ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ等。

### 应用场景

#### 1. 异步处理

场景说明：用户注册后，需要发注册邮件和注册短信。传统的做法有两种 

1.串行的方式

2.并行方式

（1）串行方式：将注册信息写入数据库成功后，发送注册邮件，再发送注册短信。以上三个任务全部完成后，返回给客户端

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/820332-20160124211106000-2080222350.png)

（2）并行方式：将注册信息写入数据库成功后，发送注册邮件的同时，发送注册短信。以上三个任务完成后，返回给客户端。与串行的差别是，并行的方式可以提高处理的时间

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/820332-20160124211115703-218873208.png)

假设三个业务节点每个使用50毫秒钟，不考虑网络等其他开销，则串行方式的时间是150毫秒，并行的时间可能是100毫秒。

因为CPU在单位时间内处理的请求数是一定的，假设CPU1秒内吞吐量是100次。则串行方式1秒内CPU可处理的请求量是7次（1000/150）。并行方式处理的请求量是10次（1000/100）

引入消息队列，将不是必须的业务逻辑，异步处理。改造后的架构如下：

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/820332-20160124211131625-1083908699.png)

按照以上约定，用户的响应时间相当于是注册信息写入数据库的时间，也就是50毫秒。注册邮件，发送短信写入消息队列后，直接返回，因此写入消息队列的速度很快，基本可以忽略，因此用户的响应时间可能是50毫秒。因此架构改变后，系统的吞吐量提高到每秒20 QPS。比串行提高了3倍，比并行提高了两倍

#### 2. 应用解耦

场景说明：用户下单后，订单系统需要通知库存系统。传统的做法是，订单系统调用库存系统的接口。如下图：

![img](http://static.codeceo.com/images/2016/02/4a63d31c026678ec4f5e4c65615524a5.png)

传统模式的缺点：

1） 假如库存系统无法访问，则订单减库存将失败，从而导致订单失败；

2） 订单系统与库存系统耦合；

如何解决以上问题呢？引入应用消息队列后的方案，如下图

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/a0d2f7ae4bc26ac1b0534660b51af7b9.png)

- 订单系统：用户下单后，订单系统完成持久化处理，将消息写入消息队列，返回用户订单下单成功。
- 库存系统：订阅下单的消息，采用拉/推的方式，获取下单信息，库存系统根据下单信息，进行库存操作。
- 假如：在下单时库存系统不能正常使用。也不影响正常下单，因为下单后，订单系统写入消息队列就不再关心其他的后续操作了。实现订单系统与库存系统的应用解耦。

#### 3. 流量削峰

流量削锋也是消息队列中的常用场景，一般在秒杀或团抢活动中使用广泛。

应用场景：秒杀活动，一般会因为流量过大，导致流量暴增，应用挂掉。为解决这个问题，一般需要在应用前端加入消息队列。

1. 可以控制活动的人数；
2. 可以缓解短时间内高流量压垮应用；

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/addea89be214fa66a0d45db711da4f91.png)

1. 用户的请求，服务器接收后，首先写入消息队列。假如消息队列长度超过最大数量，则直接抛弃用户请求或跳转到错误页面；
2. 秒杀业务根据消息队列中的请求信息，再做后续处理。

#### 4. 日志处理

日志处理是指将消息队列用在日志处理中，比如Kafka的应用，解决大量日志传输的问题。架构简化如下：

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/91a956e890623d5f05b3dac013d8dd3a.png)

- 日志采集客户端，负责日志数据采集，定时写受写入Kafka队列；
- Kafka消息队列，负责日志数据的接收，存储和转发；
- 日志处理应用：订阅并消费kafka队列中的日志数据；

#### 5. 消息通讯

消息通讯是指，消息队列一般都内置了高效的通信机制，因此也可以用在纯的消息通讯。比如实现点对点消息队列，或者聊天室等。

点对点通讯：

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/f88e45bdce945fe93c31d68df4059146.png)

客户端A和客户端B使用同一队列，进行消息通讯。

聊天室通讯：

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/61c6fd8e58722d438da19445c8016395.png)

客户端A，客户端B，客户端N订阅同一主题，进行消息发布和接收。实现类似聊天室效果。

以上实际是消息队列的两种消息模式，点对点或发布订阅模式。

### 消息中间件实例

#### 电商系统

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/820332-20160124220821515-1142658553.jpg)

消息队列采用高可用，可持久化的消息中间件。比如Active MQ，Rabbit MQ，Rocket Mq。

（1）应用将主干逻辑处理完成后，写入消息队列。消息发送是否成功可以开启消息的确认模式。（消息队列返回消息接收成功状态后，应用再返回，这样保障消息的完整性）

（2）扩展流程（发短信，配送处理）订阅队列消息。采用推或拉的方式获取消息并处理。

（3）消息将应用解耦的同时，带来了数据一致性问题，可以采用最终一致性方式解决。比如主数据写入数据库，扩展应用根据消息队列，并结合数据库方式实现基于消息队列的后续处理。

#### 日志收集系统

![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/820332-20160124220830750-1886187340.jpg)

分为Zookeeper注册中心，日志收集客户端，Kafka集群和Storm集群（OtherApp）四部分组成。

- Zookeeper注册中心，提出负载均衡和地址查找服务
- 日志收集客户端，用于采集应用系统的日志，并将数据推送到kafka队列
- Kafka集群：接收，路由，存储，转发等消息处理

Storm集群：与OtherApp处于同一级别，采用拉的方式消费队列中的数据

### 消息模型

JMS标准中，有两种消息模型P2P（Point to Point）,Publish/Subscribe(Pub/Sub)。

#### P2P（点对点）模式

![img](C:\Users\12605\Desktop\PHP_notes\.img\20151201162724900.jpg)

P2P模式包含三个角色：消息队列（Queue），发送者(Sender)，接收者(Receiver)。每个消息都被发送到一个特定的队列，接收者从队列中获取消息。队列保留着消息，直到他们被消费或超时。

P2P的特点

- 每个消息只有一个消费者（Consumer）(即一旦被消费，消息就不再在消息队列中)
- 发送者和接收者之间在时间上没有依赖性，也就是说当发送者发送了消息之后，不管接收者有没有正在运行，它不会影响到消息被发送到队列
- 接收者在成功接收消息之后需向队列应答成功

如果希望发送的每个消息都会被成功处理的话，那么需要P2P模式

#### Pub/sub模式

消息生产者（发布）将消息发布到topic中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，发布到topic的消息会被所有订阅者消费。

![img](C:\Users\12605\Desktop\PHP_notes\.img\20151201162752176.jpg)

包含三个角色主题（Topic），发布者（Publisher），订阅者（Subscriber） 多个发布者将消息发送到Topic,系统将这些消息传递给多个订阅者。

Pub/Sub的特点

- 每个消息可以有多个消费者
- 发布者和订阅者之间有时间上的依赖性。针对某个主题（Topic）的订阅者，它必须创建一个订阅者之后，才能消费发布者的消息
- 为了消费消息，订阅者必须保持运行的状态

为了缓和这样严格的时间相关性，JMS允许订阅者创建一个可持久化的订阅。这样，即使订阅者没有被激活（运行），它也能接收到发布者的消息。

如果希望发送的消息可以不被做任何处理、或者只被一个消息者处理、或者可以被多个消费者处理的话，那么可以采用Pub/Sub模型

### 流行模型对比

传统企业型消息队列ActiveMQ遵循了JMS规范，实现了点对点和发布订阅模型，但其他流行的消息队列RabbitMQ、Kafka并没有遵循JMS规范。

#### RabbitMQ

RabbitMQ实现了AQMP协议，AQMP协议定义了消息路由规则和方式。生产端通过路由规则发送消息到不同queue，消费端根据queue名称消费消息。 RabbitMQ既支持内存队列也支持持久化队列，消费端为推模型，消费状态和订阅关系由服务端负责维护，消息消费完后立即删除，不保留历史消息。

（1）点对点 生产端发送一条消息通过路由投递到Queue，只有一个消费者能消费到。

![img](C:\Users\12605\Desktop\PHP_notes\.img\20151201162841986.jpg) （2）多订阅 当RabbitMQ需要支持多订阅时，发布者发送的消息通过路由同时写到多个Queue，不同订阅组消费不同的Queue。所以支持多订阅时，消息会多个拷贝。

![img](C:\Users\12605\Desktop\PHP_notes\.img\20151201162903057.jpg)

#### Kafka

Kafka只支持**消息持久化**，消费端为拉模型，消费状态和订阅关系由客户端端负责维护，消息消费完后不会立即删除，会保留历史消息。因此支持多订阅时，消息只会存储一份就可以了。但是可能产生重复消费的情况。

（1）点对点&多订阅 发布者生产一条消息到topic中，不同订阅组消费此消息。 ![img](https://xianyunyh.gitbooks.io/php-interview/MQ/images/20151201162920224.jpg)