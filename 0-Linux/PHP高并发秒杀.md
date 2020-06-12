# `PHP高并发秒杀

---

![img](https://image-static.segmentfault.com/251/137/2511372545-5dce98853604b)

##  一、原理

### 减而治之

   - CDN原理
   - nginx限流
   - 异步队列

### 分而治之

   - nginx均衡负载

### 特征

- 写 强一致性
- 读 弱一致性

### 难点

- 极致性能的实现
- 高可用的保证

### 核心实现

- 极致性能的读服务实现
- 极致性能的写服务实现
- 极致性能的排队进度查询实现
- 链路流量优化如何做

### 高可用

- 高可用的标准
- 请求链路中每层高可用的实现原理
- 限流、一键降级、自动降级实现





## 二、基础工具及知识

### 1.测压工具ab的使用

- 安装

   ```bash
   $ yum -y install httpd-tools
   $ ab -v #版本号
   ```

- 测试接口最大qps

  ```bash
  $ ab -n100 -c10 http://xxx 
  #n次数 c并发个数
  	
  #Requests per second: 101.15 [#/sec] (mean)
  $ tail -f /var/log/nginx/error.Log
  ```

### 2.Nginx限流

#### 策略

1. 按连接数限速,即**并发数**

   `ngx.http_limit_conn.module`

2. 按请求速率限速，按照**ip**限制**单位时间内的请求数**	`ngx_http_limit_req_module`

#### 限流配置

>  /etc/nginx/nginx. conf目录下	

###### 创建规则 请求速度和连接数量

`limit_req_zone $binary_remote_addr zone=mylimit:10m rate=1r/s;`

- 区域名称为one，大小为10m，平均处理的请求频率不能超过每秒一次

###### 应用规则

`limit_req zone=mylimit burst=5 nodelay;` 

- 限制平均每秒不超过一个请求，同时允许超过频率限制的请求数不多于5个，不希望超过的请求被延迟

###### 完整实例配置

```nginx
http {
    limit_req_zone $binary_remote_addr zone=ttlsa_com:10m rate=1r/s;

    server {
        location  ^~ /download/ {  
            limit_req zone=ttlsa_com burst=5;
            alias /data/www.ttlsa.com/download/;
        }
    }
}
```

### 3.限流算法

###### 令牌桶算法

<img src="https://i.loli.net/2020/06/07/gbirdmHV2IQeLFY.png" alt="image-20200607202649623" style="zoom:50%;float:left;" />

- 匀速生产令牌，可以限制请求的速度，可以应对突发情况

###### 漏桶算法

<img src="https://i.loli.net/2020/06/07/g536eVMZ78NxSlH.png" alt="image-20200607203314678" style="zoom:50%;float:left;" />

- 请求超过时即溢出，可以强行限制请求速度

###### 计数器

- 单位时间计数器计数即可，一般在应用程序中写的较多

### 4.CDN介绍

- CDN，内容分发网络( Content Delivery Network )
- 缩短访问路径`缓存`，减少源站压力、提高内容响应速度
- 为源站提供安全保护
- 一般是腾讯云和阿里云提供的服务CDN

由DNS解析分配到不同的CDN

![image-20200607204039790](https://i.loli.net/2020/06/07/8SnPCG5hVJ9qIRb.png)

###### DNS原理-普通域名访问

<img src="https://i.loli.net/2020/06/07/IolswAnvS5mUV93.png" alt="image-20200607204404947" style="zoom: 50%; float: left;" />

- 根据域名等级，一级一级地查询访问

![image-20200607204450229](https://i.loli.net/2020/06/07/BUGEvZxzjAadIlM.png)

- DNS服务器数据存储格式

  | 域名       | Class | 类型  | 数据            |
  | ---------- | ----- | ----- | --------------- |
  | a.com      | IN    | A     | 10.10.xx.xx     |
  | mail.a.com | IN    | MX    | 10.10.xx.xx     |
  | cdn.a.com  | IN    | CNAME | cdn.cdntip.com. |

  

> `CNAME记录`——类似查询转发，该记录不能直接使用IP ，只能是另一个主机
> 的别名。CDN是利用该记录来指定cnd服务器，如果有A记录与CNAME记
> 录同时存在，则只使用A记录
 - 通过CNAME请求最近的CDN服务器
![image-20200607205119392](https://i.loli.net/2020/06/07/XRL7doGe6chPUYs.png)

### 5.nginx负载均衡算法

###### 大型网站架构

![image-20200607205458305](https://i.loli.net/2020/06/07/mvjCphug4Q6HtJG.png)

> 网站架构主要实现`高并发`，`高可用`，每层之间进行心跳检测，若有宕机则剔除这个服务
>
>  `LVS`（Linux Virtual Server）即Linux虚拟服务器，是由章文嵩博士主导的开源负载均衡项目，目前LVS已经被集成到Linux内核模块中。该项目在Linux内核中实现了基于IP的数据请求负载均衡调度方案，其体系结构如图1所示，终端互联网用户从外部访问公司的外部负载均衡服务器，终端用户的Web请求会发送给LVS调度器，调度器根据自己预设的算法决定将该请求发送给后端的某台Web服务器，比如，轮询算法可以将外部的请求平均分发给后端的所有服务器，终端用户访问LVS调度器虽然会被转发到后端真实的服务器，但如果真实服务器连接的是相同的存储，提供的服务也是相同的服务，最终用户不管是访问哪台真实服务器，得到的服务内容都是一样的，整个集群对用户而言都是透明的。最后根据LVS工作模式的不同，真实服务器会选择不同的方式将用户需要的数据发送到终端用户，LVS工作模式分为NAT模式、TUN模式、以及DR模式。

###### Round-robin (轮询)

- 根据Nginx配置文件中的顺序，依次把客户端的Web请求分发到不同的后端服务器。每个请求按`时间顺序`逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

  ```nginx
  upstream web {
      server server1;
      server server2;
  }
  ```

  

###### Weight-round-robin (带权轮循)

- 可以根据`服务器的性能状况`有选择的分发web请求。指定轮询几率`每次加权`，weight越高、访问比率越大。weight=2，意味着每接收到3个请求，前2个请求会被分发到第一个服务器，第3个请求会分发到第二个服务器，其它的配置同轮询配置

  ```nginx
  upstream web{
  	server serverl weight=2;
  	server server2 weight=1;
  }
  ```

  

###### Ip-hash (Ip哈希)

- 同一客户端连续的Web请求可能会被分发到不同的后端服务器进行处理，因此如果涉及到`会话Session`，可以使用基于IP地址哈希的负载均衡方案。

  这样的话，`同一客户端连续的Web请求`都会被分发到同一服务器进行处理（每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题）。

  ```nginx
  upstream web {
      ip_hash;
      server server1;
      server server2;
  }
  ```

  

### 6.消息队列

消息队列实际为`链表`，头插尾出，高并发下容易发生堵塞为避免消息丢失，可通过写入实时消息队列进行`延时处理`。

- 实时队列

  主要根据请求的队列入队，先进先出

- 延时队列

  根据请求需要过期的时间依次放入队列对应位置

  > 应用场景：
  >
  > - 订单超过 30 分钟未支付，则自动取消。
  >
  > - 外卖商家超时未接单，则自动取消。
  >
  > - 医生抢单电话点诊，超过 30 分钟未打电话，则自动退款。
  >
  >   等等场景都可以用定时任务去轮询实现，但是当数据量过大的时候，高频轮询数据库会消耗大量的资源，此时用延迟队列来应对这类场景比较好

###### 作用

- 提高请求响应速度，如:创建订单后的流程，发push、短信提醒等
- 瞬间高并发下，可起到削峰，如:双十一零点并发创建订单
- 延时队列，时间维度任务触发，如:发货提醒

## 三、秒杀系统

### 难点

秒杀系统使用场景

| 场景                           | 并发量                    |
| ------------------------------ | ------------------------- |
| 商城活动抢购、优惠券、定时抢购 | 有效写100+  并发抢1w+     |
| 小米商城手机抢购               | 有效写1W+   并发抢100w+   |
| 12306的抢票                    | 有效写1W+    并发抢100w+  |
| 天猫双十一凌晨促销秒杀         | 有效写10w+   并发抢1000w+ |

### 特点

- 抢购人数远多于库存，读写并发巨大
- 库存少，有效写少
- `写`需`强一致性`，商品不能卖超
- `读`强一致性要求不高

### 难点

###### 稳定难

- 高并发下，某个`小依赖`可能直接造成雪崩
- 流量预期难精确，过高也造成雪崩
- 分布式集群，`机器`多，出故障的概率高

###### 准确难

- 库存、抢购成功数、创建订单数之间的一致性

###### 高性能

- 有限成本下需要做到极致的性能

### 架构原则

###### 稳定性

- 减少第三方依赖，同时自身服务部署也需做到隔离
- 压测、降级、限流方案、确保核心服务可用
- 需健康度检查机制，整个链路避免单点

###### 高性能

- 缩短单请求访问`路径`、减少`IO`
- 减少`接口数`、降低吞吐数据量、请求次数减少

## 四、秒杀系统核心实现

- 满足基本需求，做到单服务极致性能
- 请求链路流量优化，从客户端到服务端每层优化
- 稳定性建设

###### 基本需求

- 扣库存
- 查库存、排队进度
- 查订单详情、创建订单、支付订单

> 其中扣库存和查库存、排队进度需要做到单服务极致性能
>
> 因为库存少抢购人数远多于库存，读写并发高

###### 场景示例

- 某件商品有1000库存
- 100w并发读
- 100w并发抢购

### 扣库存

###### 扣库存方案

![image-20200608123609802](https://i.loli.net/2020/06/08/1tyh6RvAaOwSPV7.png)

> 预扣库存实现方案:
>
> - 先扣除库存，然后创建订单、支付
> - 10分钟内不支付则取消订单，避免不支付库存卖不出问题

######  极高并发下怎么做到单服务极致性能?

**有效压榨CPU资源**

- 减少上下文切换

  进程切换、线程切换、系统调用（用户态核心态切换）

  fpm是阻塞模型，会经常会出现进程切换

- 减少阻塞式I/O

  rpc调用 远程IO通信
  磁盘读写

###### 无IO怎么做

![image-20200608124941276](https://i.loli.net/2020/06/08/EYCZD5WQnzhebOM.png)

- 拆解	`扣库存`与`写订单`分开 
- 用内存

- 用本地内存

![image-20200608125126849](https://i.loli.net/2020/06/08/yfuCho4lr6nwzZO.png)

预留buf，统一减库存

![image-20200608152659791](https://i.loli.net/2020/06/08/JLNu5Hxk8f1bBUd.png)

###### 实现统一减库存  防止超卖和少卖

1. 初始化库存到本地库存
2. 本地减库存，成功则进行统一减库存 ，失败则返回
3. 统一减库存成功则写入MQ ，异步创建订单
4. 告知用户抢购成功

###### 扣库存代码

```php
include_once 'base.php';

class Api extends Base
{
    //共享信息存储在redis中，一hash表形式存储，%s变量代表的是商品id
    static $userId;
    static $productId;

    static $REDIS_REMOTE_HT_KEY = 'product_%s';             //共享信息key
    static $REDIS_REMOTE_TOTAL_COUNT = 'total_count';      //商品总库存
    static $REDIS_REMOTE_USE_COUNT = 'used_count';       //已售库存
    static $REDIS_REMOTE_QUEUE = 'c_order_queue';           //创建订单队列

    static $APCU_LOCAL_STOCK = 'apcu_stock_%s';              //总共剩余库存
    static $APC_LOCAL_USE = 'apcu_stock_use_%s';            //本地已售库存
    static $APC_LOCAL_COUNT = 'apcu_stock_count_%s';        //本地分库分摊总数

    public function __construct($productId, $userId)
    {
        self::$REDIS_REMOTE_HT_KEY = sprintf(self::$REDIS_REMOTE_HT_KEY, $productId);
        self::$APCU_LOCAL_STOCK = sprintf(self::$APCU_LOCAL_STOCK, $productId);
    }

    //清空缓存
    public function clear()
    {
        //var_export(apcu_cache_info()); //运行时缓存
        apcu_clear_cache();
        self::output();
    }
    //数据同步
    public function sync()
    {
        $res = 0;
        $localUse = apcu_fetch(self::$APC_LOCAL_USE);

        //与redis同步，redis库的存量和已售减去本地缓存预售的量，去本地化
        if($localUse > 0){
            showInfo();
            $script = <<<eof
    local key = KEYS[1]
    local field1 = KEYS[2]
    local field2 = KEYS[3]
    local field1_res = redis.call('HINCRBY', key, field1, 0 - ARGV[1])
    if(field1_res) then
        return redis.call('HINCRBY', key, field2, 0 - ARGV[1])
    end
    return 0
eof;
            $res = self::conRedis()->eval($script, [
                self::$REDIS_REMOTE_HT_KEY,
                self::$REDIS_REMOTE_TOTAL_COUNT,
                self::$REDIS_REMOTE_USE_COUNT,
                $localUse
            ], 3);
            apcu_store(self::$APC_LOCAL_USE, 0);
            apcu_dec(self::$APCU_LOCAL_STOCK, $localUse); //本地剩余库存
        }
        self::output([$res,$localUse], 0, '同步成功');
    }

    //查询库存
    public function getStock()
    {
        $stockNum = apcu_fetch(self::$APCU_LOCAL_STOCK);
        var_dump(self::$APCU_LOCAL_STOCK. '-2', $stockNum);
        if ($stockNum === false){
            $stockNum = self::initStock();
        }
        self::output(['stock_num'=>$stockNum]);
    }

    // 抢购-减库存
    public function buy()
    {	//是否有本地库存 没有则获取
        $localStockNum = apcu_fetch(self::$APCU_LOCAL_STOCK);
        if ($localStockNum === false){
            $localStockNum = self::init();
        }

        $localUse = apcu_inc(self::$APC_LOCAL_USE); //已卖 +1
        if($localUse > $localStockNum){ //抢购失败，大部分流量分流拦截在此
            self::output([1], -1, '该商品已售完');
        }

        //同步已售库存 +1
        if(!$this->incUseCount()){ //如果改失败，返回已售完
            self::output(['*'], -1, '该商品已售完');
        }

        //写入创建订单队列
        self::conRedis()->lPush(self::$REDIS_REMOTE_QUEUE, json_encode([
            'user_id' => self::$userId,
            'product_id' => self::$productId
        ]));
        //返回抢购成功
        self::output([], 0, '抢购成功，请从订单中心查看订单');
    }

    //初始化本地数据
    static function init()
    {
        apcu_add(self::$APC_LOCAL_USE, 0);
        return 0;
    }
    static function initStock()
    {
        $data = self::conRedis()->hMGet(self::$REDIS_REMOTE_HT_KEY,
            [self::$REDIS_REMOTE_TOTAL_COUNT, self::$REDIS_REMOTE_USE_COUNT, 'server_num']
        );
        $num = $data[self::$REDIS_REMOTE_TOTAL_COUNT] - $data[self::$REDIS_REMOTE_USE_COUNT];
        $stock = round($num / ($data['server_num'] - 1));
        apcu_add(self::$REDIS_REMOTE_TOTAL_COUNT, $data[self::$REDIS_REMOTE_TOTAL_COUNT]); //总剩余库存
        apcu_add(self::$APCU_LOCAL_STOCK, $num); //总剩余库存分布到本地额度
        var_dump(self::$APCU_LOCAL_STOCK .'-1', $stock);
        return $stock;
    }

    //私有方法，库存同步: 给总预售数 +1
    private function incUseCount()
    {
        //同步远端库存时，需要经过lua脚本，保证不会出现超卖现象
        $script = <<<eof
    local key = KEYS[1]
    local field1 = KEYS[2]
    local field2 = KEYS[3]
    local field1_val = redis.call('hget', key, field1) + 0
    local field2_val = redis.call('hget', key, field2) + 0
    if(field1_val > field2_val) then
        return redis.call('HINCRBY', key, field2, 1)
    end
    return 0
eof;
        /*var_dump(self::$REDIS_REMOTE_HT_KEY,
            self::conRedis()->eval("return redis.call('hgetall','product_1')"),
            self::conRedis()->eval($script, [
                self::$REDIS_REMOTE_HT_KEY,
                self::$REDIS_REMOTE_TOTAL_COUNT,
                self::$REDIS_REMOTE_USE_COUNT
            ], 3)
        );exit;*/
        return self::conRedis()->eval($script, [
            self::$REDIS_REMOTE_HT_KEY,
            self::$REDIS_REMOTE_TOTAL_COUNT,
            self::$REDIS_REMOTE_USE_COUNT
        ], 3);
    }
}



echo '<pre>';
parse_str($_SERVER['QUERY_STRING'], $req);
if($req['act'] && $req['product_id'] && $req['user_id']){
    $method = $req['act'];
    print_r($req);
    $rm = new \ReflectionMethod('Api',$method);
    if($rm->isStatic()){
        (new Api($req['product_id'], $req['user_id']))::$method();
    }else{
        (new Api($req['product_id'], $req['user_id']))->$method();
    }
}else{
    echo '初始化---------<br/>';
    apcu_clear_cache();
    showInfo();

    $data = [$req['product_id'] ?? 1, $req['user_id'] ?? 1];
    $key = sprintf(Api::$REDIS_REMOTE_HT_KEY, $data[1]);
    $res = Base::conRedis()->hMset($key, [Api::$REDIS_REMOTE_TOTAL_COUNT=>1000, Api::$REDIS_REMOTE_USE_COUNT=>0, 'server_num'=>3]);
    var_dump($res);
    (new Api($data[0], $data[1]))::initStock();
    Base::output();
}

echo '<pre>';
parse_str($_SERVER['QUERY_STRING'], $req);
showInfo();
if($req['act'] && $req['product_id'] && $req['user_id']){
    $method = $req['act'];
    print_r($req);
    $rm = new \ReflectionMethod('Api',$method);
    if($rm->isStatic()){
        (new Api($req['product_id'], $req['user_id']))::$method();
    }else{
        (new Api($req['product_id'], $req['user_id']))->$method();
    }
}else{
    echo '初始化---------<br/>';
    apcu_clear_cache();

    $data = [$req['product_id'] ?? 1, $req['user_id'] ?? 1];
    $key = sprintf(Api::$REDIS_REMOTE_HT_KEY, $data[1]);
    $res = Base::conRedis()->hMset($key, [Api::$REDIS_REMOTE_TOTAL_COUNT=>1000, Api::$REDIS_REMOTE_USE_COUNT=>0, 'server_num'=>3]);
    var_dump($res);
    (new Api($data[0], $data[1]))::initStock();
    Base::output();
}

function showInfo(){
    $serverInfo = [
        'gethostbyname'=>gethostbyname(gethostname().'.'),
        'REQUEST_TIME'=>date("Y-m-d H:i:s", $_SERVER['REQUEST_TIME']),
        'HTTP_COOKIE'=>$_SERVER['HTTP_COOKIE'],
        'HTTP_HOST'=>$_SERVER['HTTP_HOST'],
        'SERVER_SOFTWARE'=>$_SERVER['SERVER_SOFTWARE'],
        'SERVER_ADDR'=>$_SERVER['SERVER_ADDR'],
        'SERVER_PORT'=>$_SERVER['SERVER_PORT'],
        'REMOTE_ADDR'=>$_SERVER['REMOTE_ADDR'],
        'REMOTE_PORT'=>$_SERVER['REMOTE_PORT'],
    ];
    var_export(array_merge_recursive(['session_id'=>session_id()], $serverInfo));
}
```

###### 创建、支付订单

![image-20200608155704861](C:\Users\12605\AppData\Roaming\Typora\typora-user-images\image-20200608155704861.png)

###### 读商品信息页

- 与库存服务隔离
- 商品库：一主多从提高读能力
- 页面静态化+缓存+db实现即可

# 高并发

## 高并发大流量解决方案

### 高并发大流量相关概念

#### 高并发

在互联网时代，所讲的高并发，通常是指并发访问。也就是在某个时间点，有多少
个访问同时到来。
通常如果一个系统的日`PV`在`千万`以上，有可能是一个高井发的系统有的公司完全不走技术路线。全靠机器堆，这不在我们的讨论范围。

#### 一些参数

- `QPS`

  每秒钟请求或者查询的数量，在互联网领域，指`每秒响应请求数`(指`HTTP请求`)

- `吞吐量`

  ​	单位时间内处理的请求数量(通常由QPS与并发数决定)

- `响应时间`

  ​	从请求发出到收到响应花费的时间.例如系统处理一个HTTP请求需要100ms ,这个100ms就是系统的响应时间

- `PV`

  ​	综合浏览量( Page View )， 即页面浏览量或者点击量，一个访客在24小时内访问的页面数量
  ​	同一个人浏览你的网站同一页面，只记作一次PV

- `UV `

  ​	独立访客( UniQue Visitor) ， 即一定时间范围内`相同`访客`多次`访问网站，只计算为1个独立访客

- `带宽`

  ​	计算带宽大小需关注两个指标， 峰值流量和页面的平均大小
  ​	`日网站带宽`= `PV` /`统计时间(换算到秒)` \*`平均页面大小(单位KB)`*8
  ​	峰值一般是平均值的倍数， 根据实际情况来定

- `QPS`≠`并发连接数`

  ​	(总PV数* 80% )/( 6小时秒数* 20% )=峰值每秒请求数(QPS)
  ​	80%的访问量集中在20%的时间

#### 压力测试

- 测试能承受的最大并发

- 测试最大承受的QPS值

- `ab`
  全称是apache benchmark ，是apache官方推出的工具

  原理：创建多个并发访问线程，模拟多个访问者同时对某一URL地址进行访问。

  它的测试目标是基于URL的，因此，它既可以用来测试apache的负载压力，也可以测试nginx等其它Web服务器的压力。

  ```bash
  #模拟并发请求100次,总共请求5000次
  $ab -c 100 -n 5000 待测试网站
  ```

  - **注意事项**

    测试机器与被测试机器分开
    不要对线上服务做压力测试
    观察测试工具ab所在机器,以及被测试的前端机的CPU，内存，网络等都不超过最高限度的75%

#### QPS各量级举例解决方案

- QPS达到**50**：小型网站量级，一般的服务器就可以应付。

- QPS达到**100**：假设关系型数据库的每次请求在0.01秒完成，且单页面只有一个SQL查询，那么100QPS意味这1秒钟完成100次请求，但是此时我们并不能保证数据库查询能完成100次。

  方案：`数据库缓存层`、`数据库的负载均衡`

- QPS达到**800**：假设我们使用百兆带宽，意味着网站出口的实际带宽是8M左右，且每个页面只有10k，在这个并发条件下，百兆带宽已经占满。

  方案：`CDN加速`、`负载均衡`

- QPS达到**1000**：假设使用Redis缓存数据库查询数据，每个页面对Redis的请求远大于直接对DB的请求，Redis的悲观并发数在3W左右，但有可能在之前内网带宽已经吃光，表现出不稳定。

  方案：`静态HTML缓存`

- QPS达到**2000**：这个级别下，文件系统访问锁都成为灾难。

  方案：`做业务分离`，`分布式存储`

### 高并发解决方案

| 业务场景 | 解决措施           |                                                              |
| -------- | ------------------ | ------------------------------------------------------------ |
| 前端：   | 应用和静态资源分离 | 静态资源主要包括图片、视频、js、css和一些资源文件，直接存放到响应的服务器使用专门的域名去访问就可以了 |
|          | 页面缓存           | 将应用生成的页面缓存起来，可以节省大量的CPU资源，尽可能采用静态页面来实现功能 |
|          | CDN进行内容分发    | CDN 服务器是分布在全国各地的，当接收到用户请求后会将请求分配到最合适的CDN服务器节点获取数据。 |
|          |                    | 独立图片服务器、异步请求                                     |
| 后端：   | 请求队列           | nginx限流、分流、消息队列                                    |
|          | 共享缓存           | 公共数据缓存，共享缓存服务。                                 |
|          | 分布式             | 当单机无法解决时，增加服务器数量(集群)，通过反向代理、负载均衡，将请求分发分流。 |
|          | 微服务化           | 将不同的业务放到不同的服务器中，分布式架构。一个请求可能需要用到多台服务器，这样就可以提高一个请求的处理速度，而且集群和分布式也可以同时使用。 |
|          | 代码               | 关注代码本身，防止出现内存溢出的情况，优化查询语句，避免系统频繁请求数据库，合理使用索引，减少sql语句执行的时间。逻辑代码优化、算法优化 |
| 数据层： |                    | 数据水平分割(分区分表分库)、读写分离、redis缓存、写队列      |
| 存储：   |                    | raid阵列、热备                                               |
| 网络：   |                    | dns轮询、DDOS攻击防护、防盗链处理                            |

## web资源防盗链

防盗链概念

工作原理

实现方法

## 减少HTTP请求

## 浏览器缓存和压缩



## CDN加速

## 建立独立的图片服务器



## 动态语言静态化



## 动态语言层的并发处理



## 数据库缓存层优化



## MySQL数据层优化



## Web服务器的负载均衡、请求分发