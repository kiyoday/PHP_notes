# 相关配置

## 	环境配置

- 框架选择- thinkphp5.1
- swoole特性使用
- `赛事直播平台`
- nginx负载均衡
- redis
- `系统监控+性能优化`

下载thinkphp5.1

### 导入静态文件

​	将html文件资源放入public/static/live下

### 开启swoole http server

​	新建server目录，使用http_server.php的代码

用swoole访问

## Swoole适配TP框架

原因

> 由于TP框架是用`request类`来接受参数的，
>
> 而原生PHP是`$_SERVER`魔术变量来接受请求参数的

- Swoole_http配置入口文件 加载框架

> `onWorkerStart`
> 此事件在 Worker 进程 / Task 进程启动时发生，这里创建的对象可以在进程生命周期内使用。
>
> `function onWorkerStart(Swoole\Server $server, int $workerId);`

```php
//WorkerStart在框架初始阶段加载
$http->on('WorkerStart' ,function ($server, $worker_id) {
    // 定义常量 应用目录
    define('APP_PATH', __DIR__ . '/../application/');
    // 加载TP的基础文件
    require __DIR__ . '/../thinkphp/base.php';
});
```

- 因为`swoole接收参数`和thinkphp中接收不一样，所以需要转换为thinkphp可识别，转换POST参数示例如下:

`thinkphp\script\bin\server\http_server.php`

### 使用swoole的http服务器

代理全局变量 后面会进行封装

```php
$http = new swoole_http_server("0.0.0.0", 8811);

$http->set(
    [
        'enable_static_handler' => true,
        //静态文件支持
        'document_root' => "/home/work/hdtocs/swoole_mooc/thinkphp/public/static",
        'worker_num' => 5,
    ]
);
//加载框架写到下面request中会跟原生fpm一样 性能会有损耗但是不用修改代码
$http->on('WorkerStart', function(swoole_server $server,  $worker_id) {
    // 定义应用目录
    define('APP_PATH', __DIR__ . '/../application/');
    // 加载框架里面的文件
    require __DIR__ . '/../thinkphp/base.php';
    //require __DIR__ . '/../thinkphp/start.php';
});

//代理全局变量
$http->on('request', function ($request, $response) {
    //这种形式 不用修改框架
    //define('APP_PATH', __DIR__ . '/../application/');
    //require __DIR__ . '/../thinkphp/base.php';
    $_SERVER  =  [];
    if(isset($request->server)) {
        foreach($request->server as $k => $v) {
            $_SERVER[strtoupper($k)] = $v;
        }
    }
    if(isset($request->header)) {
        foreach($request->header as $k => $v) {
            $_SERVER[strtoupper($k)] = $v;
        }
    }

    $_GET = [];
    if(isset($request->get)) {
        foreach($request->get as $k => $v) {
            $_GET[$k] = $v;
        }
    }
    $_POST = [];
    if(isset($request->post)) {
        foreach($request->post as $k => $v) {
            $_POST[$k] = $v;
        }
    }

    ob_start();
    // 执行应用并响应
    try {
        think\Container::get('app')->run()->send();
    }catch (\Exception $e) {
        // todo
    }

    //echo "-action-".request()->action().PHP_EOL;
    $res = ob_get_contents();
    ob_end_clean();
    $response->end($res);
    //$http->close(); //会报错重启
});
```

开启服务器

```bash
$ php http_server.php
```



- **解决每次路由访问显示第一次访问时的路径信息**

  找到thinkphp/library/think/`Request.php`文件
  function path 中的if (is_null($this->path)) {}注释或删除  里面的内容不动

  function pathinfo中的if (is_null($this->pathinfo)) {}注释或删除  里面的内容不动

# 登录模块

## 登录模块

- 登录流程 示意图

  <img src="https://i.loli.net/2020/06/07/ePxNVgjTOa3HK29.png" alt="image-20200607123936591" style="zoom: 100%;float:left" />

### 发送验证码 send类

```php
class Send
{
    /**
     * 发送验证码
     */
    public function index() {
        // tp方式  input
        //$phoneNum = request()->get('phone_num', 0, 'intval');
        //原生方式
        $phoneNum = intval($_GET['phone_num']);
        if(empty($phoneNum)) {
            // status 0 1  message data
            return Util::show(config('code.error'), 'error');
        }

        //tood
        // 生成一个随机数
        $code = rand(1000, 9999);
		//task 异步任务
        $taskData = [
            'method' => 'sendSms',
            'data' => [
                'phone' => $phoneNum,
                'code' => $code,
            ]
        ];
        //task 异步任务 $_POST['http_server'] 由http.php 抛过来
        $_POST['http_server']->task($taskData);
        //return Util::show(config('code.success'), 'ok');
        try {
            //Sms::sendSms 发送短信SDK
            $response = Sms::sendSms($phoneNum, $code);
        }catch (\Exception $e) {
            // todo
            return Util::show(config('code.error'), '阿里大于内部异常');
        }
        
        if($response->Code === "OK") {
            //协程 Redis 客户端  每次都需要实例化 后续会优化
            $redis = new \Swoole\Coroutine\Redis();
            $redis->connect(config('redis.host'), config('redis.port'));
            //设置 string类型
            $redis->set(Redis::smsKey($phoneNum), $code, config('redis.out_time'));
			
            
            return Util::show(config('code.success'), 'success');
        } else {
            return Util::show(config('code.error'), '验证码发送失败');
        }
    }
}
```

Api格式化

```php
namespace app\common\lib;
class Util {

    /**
     * API 输出格式
     * @param $status
     * @param string $message
     * @param array $data
     */
    public static function show($status, $message = '', $data = []) {
        $result = [
            'status' => $status,
            'message' => $message,
            'data' => $data,
        ];

        echo json_encode($result);
    }
}
```

config/redis

```php
<?php
/**
 * redis相关配置
 * Created by PhpStorm.
 * User: baidu
 * Date: 18/3/23
 * Time: 上午9:17
 */

return [
    'host' => '127.0.0.1',
    'port' => 6379,
    'out_time' => 120,
    'timeOut' => 5, // 超时时间
    'live_game_key' => 'live_game_key'
];
```

### 封装predis

单例模式避免每次链接redis

```php
<?php

namespace app\common\lib\redis;

class Predis {
    public $redis = "";
    /**
     * 定义单例模式的变量
     * @var null
     */
    private static $_instance = null;

    public static function getInstance() {
        if(empty(self::$_instance)) {
            self::$_instance = new self();
        }
        return self::$_instance;
    }

    private function __construct() {
        $this->redis = new \Redis();
        $result = $this->redis->connect(config('redis.host'), config('redis.port'), config('redis.timeOut'));
        if($result === false) {
            throw new \Exception('redis connect error');
        }
    }

    /**
     * set
     * @param $key
     * @param $value
     * @param int $time
     * @return bool|string
     */
    public function set($key, $value, $time = 0 ) {
        if(!$key) {
            return '';
        }
        if(is_array($value)) {
            $value = json_encode($value);
        }
        if(!$time) {
            return $this->redis->set($key, $value);
        }

        return $this->redis->setex($key, $time, $value);
    }

    /**
     * get
     * @param $key
     * @return bool|string
     */
    public function get($key) {
        if(!$key) {
            return '';
        }

        return $this->redis->get($key);
    }

    /**
     * @param $key
     * @return array
     */
    public function sMembers($key) {
        return $this->redis->sMembers($key);
    }

    /**
     * @param $name
     * @param $arguments
     * @return array
     */
    public function __call($name, $arguments) {
        //echo $name.PHP_EOL;
        //print_r($arguments);
        if(count($arguments) != 2) {
            return '';
        }
        $this->redis->$name($arguments[0], $arguments[1]);
    }
}
```

### 登录方法

```php
<?php
namespace app\index\controller;

use app\common\lib\Util;
use app\common\lib\Redis;
use app\common\lib\redis\Predis;
class Login
{
    public function index() {
        // phone code
        $phoneNum = intval($_GET['phone_num']);
        $code = intval($_GET['code']);
        if(empty($phoneNum) || empty($code)) {
            return Util::show(config('code.error'), 'phone or code is error');
        }

        // redis code
        try {
            $redisCode = Predis::getInstance()->get(Redis::smsKey($phoneNum));
        }catch (\Exception $e) {
            echo $e->getMessage();
        }
        if($redisCode == $code) {
            // 写入redis
            $data = [
                'user' => $phoneNum,
                'srcKey' => md5(Redis::userkey($phoneNum)),
                'time' => time(),
                'isLogin' => true,
            ];
            Predis::getInstance()->set(Redis::userkey($phoneNum), $data);

            return Util::show(config('code.success'), 'ok', $data);
        } else {
            return Util::show(config('code.error'), 'login error');
        }
        // redis.so
    }
}
```

## 基于swoole优化

### http服务封装 代替原来的server

- 抛出`$this->http` 

- 分发task任务

```php
<?php
/**
 * Created by PhpStorm.
 * User: baidu
 * Date: 18/3/27
 * Time: 上午12:50
 */
class Http {
    CONST HOST = "0.0.0.0";
    CONST PORT = 8811;

    public $http = null;
    public function __construct() {
        $this->http = new swoole_http_server(self::HOST, self::PORT);

        $this->http->set(
            [
                'enable_static_handler' => true,
                'document_root' => "/home/work/hdtocs/swoole_mooc/thinkphp/public/static",
                'worker_num' => 4,
                'task_worker_num' => 4,
            ]
        );
		//下面定义该类 的 回调函数
        $this->http->on("workerstart", [$this, 'onWorkerStart']);
        $this->http->on("request", [$this, 'onRequest']);
        $this->http->on("task", [$this, 'onTask']);
        $this->http->on("finish", [$this, 'onFinish']);
        $this->http->on("close", [$this, 'onClose']);

        $this->http->start();
    }
	
    /**
     * @param $server
     * @param $worker_id
     */
    public function onWorkerStart($server,  $worker_id) {
        // 定义应用目录
        define('APP_PATH', __DIR__ . '/../application/');
        // 加载框架里面的文件
        //require __DIR__ . '/../thinkphp/base.php';
        require __DIR__ . '/../thinkphp/start.php';
    }

    /**
     * request回调
     * @param $request
     * @param $response
     */
    public function onRequest($request, $response) {
        $_SERVER  =  [];
        if(isset($request->server)) {
            foreach($request->server as $k => $v) {
                $_SERVER[strtoupper($k)] = $v;
            }
        }
        if(isset($request->header)) {
            foreach($request->header as $k => $v) {
                $_SERVER[strtoupper($k)] = $v;
            }
        }

        $_GET = [];
        if(isset($request->get)) {
            foreach($request->get as $k => $v) {
                $_GET[$k] = $v;
            }
        }
        $_POST = [];
        if(isset($request->post)) {
            foreach($request->post as $k => $v) {
                $_POST[$k] = $v;
            }
        }

        $_POST['http_server'] = $this->http;

        ob_start();
        // 执行应用并响应
        try {
            think\Container::get('app', [APP_PATH])
                ->run()
                ->send();
        }catch (\Exception $e) {
            // todo
        }

        $res = ob_get_contents();
        ob_end_clean();
        $response->end($res);
    }

    /**
     * @param $serv
     * @param $taskId
     * @param $workerId
     * @param $data
     */
    public function onTask($serv, $taskId, $workerId, $data) {
        
        //发送短信 任务 需要封装 否则会有点乱
        /*$obj = new app\common\lib\ali\Sms();
        try {
            $response = $obj::sendSms($data['phone'], $data['code']);
        }catch (\Exception $e) {
            // todo
            echo $e->getMessage();
        }*/
        
        //封装后 。。。
        
        // 分发 task 任务机制，让不同的任务 走不同的逻辑
        $obj = new app\common\lib\task\Task;

        $method = $data['method'];//sendSms
        //Task->sendSms($data['data'])
        $flag = $obj->$method($data['data']); 

        return $flag; // 告诉worker
    }

    /**
     * @param $serv
     * @param $taskId
     * @param $data
     */
    public function onFinish($serv, $taskId, $data) {
        echo "taskId:{$taskId}\n";
        echo "finish-data-sucess:{$data}\n";
    }

    /**
     * close
     * @param $ws
     * @param $fd
     */
    public function onClose($ws, $fd) {
        echo "clientid:{$fd}\n";
    }
}

new Http();
```

send类中 分发task任务

```php
//task 异步任务
$taskData = [
    'method' => 'sendSms',
    'data' => [
        'phone' => $phoneNum,
        'code' => $code,
    ]
];
//task 异步任务 $this->http $_POST['http_server'] 由http.php 抛过来
$_POST['http_server']->task($taskData);
```



### task异步 任务 将send短信放在任务当中

```php
<?php
/**
 * 代表的是  swoole里面 后续 所有  task异步 任务 都放这里来
 * Date: 18/3/27
 * Time: 上午1:20
 */
namespace app\common\lib\task;
use app\common\lib\ali\Sms;
use app\common\lib\redis\Predis;
use app\common\lib\Redis;
class Task {

    /**
     * 异步发送 验证码
     * @param $data
     * @param $serv swoole server对象
     */
    public function sendSms($data, $serv) {
        try {
            $response = Sms::sendSms($data['phone'], $data['code']);
        }catch (\Exception $e) {
            // todo
            return false;
        }

        // 如果发送成功 把验证码记录到redis里面
        if($response->Code === "OK") {
            Predis::getInstance()->set(Redis::smsKey($data['phone']), $data['code'], config('redis.out_time'));
        }else {
            return false;
        }
        return true;
    }

    /**
     * 通过task机制发送赛况实时数据给客户端
     * @param $data
     * @param $serv swoole server对象
     */
    public function pushLive($data, $serv) {
        $clients = Predis::getInstance()->sMembers(config("redis.live_game_key"));

        foreach($clients as $fd) {
            $serv->push($fd, json_encode($data));
        }
    }
}
```



