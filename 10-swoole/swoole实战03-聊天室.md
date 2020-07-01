## 聊天室

客户端抛送给服务器

### websocket 监听新端口

```php
class Ws {
    CONST HOST = "0.0.0.0";
    CONST PORT = 8811;
    CONST CHART_PORT = 8812;

    public $ws = null;
    public function __construct() {
        // 获取 key 有值 del
        $this->ws = new swoole_websocket_server(self::HOST, self::PORT);
        //监听新端口
        $this->ws->listen(self::HOST, self::CHART_PORT, SWOOLE_SOCK_TCP);
```

### 聊天室实现

```php
class Chart
{
    public function index()
    {
        // TODO 登录验证 数据校验
        if(empty($_POST['game_id'])) {
            return Util::show(config('code.error'), 'error');
        }
        if(empty($_POST['content'])) {
            return Util::show(config('code.error'), 'error');
        }
		//TODO 获取登录信息
        $data = [
            'user' => "用户".rand(0, 2000),
            'content' => $_POST['content'],
        ];
        //  todo
        foreach($_POST['http_server']->ports[1]->connections as $fd) {
            $_POST['http_server']->push($fd, json_encode($data));
        }

        return Util::show(config('code.success'), 'ok', $data);
    }
}
```

### 客户端会有两个连接

![image-20200701110204515](C:\Users\12605\Desktop\PHP_notes\.img\image-20200701110204515.png)

# 系统监控与性能优化模块

## 监控

Linux	+ swoole定时器	+	PHPshell执行命令

```bash
$ netstat -anp | grep 8811 
$ crontab 定时任务
```

#### 监控服务

```php
<?php
/**
 * 监控服务 ws http 8811
 * Created by PhpStorm.
 * User: baidu
 * Date: 18/4/7
 * Time: 下午10:00
 */

class Server {
    const PORT = 8811;

    public function port() {
        $shell  =  "netstat -anp 2>/dev/null | grep ". self::PORT . " | grep LISTEN | wc -l";
		//php原生方法
        $result = shell_exec($shell);
        if($result != 1) {
            //todo 发送报警服务 邮件 短信
            echo date("Ymd H:i:s")."error".PHP_EOL;
        } else {
            echo date("Ymd H:i:s")."succss".PHP_EOL;
        }
    }
}

// nohup
swoole_timer_tick(2000, function($timer_id) {
    (new Server())->port();
    echo "time-start".PHP_EOL;
});

```

```bash
#后台运行并输出日志
$ nohup php server.php > ./a.log &#后台运行符号

#查看检控脚本是否运行
$ ps aux | grep monitor/server.php
#查看日志
$ tail -f a.log

#监控机器硬件
$ df -h
```

### 记录日志

```php
/**
* 记录日志
*/
public function writeLog() {
    $datas = array_merge(['date' => date("Ymd H:i:s")]
                         ,$_GET
                         , $_POST
                         , $_SERVER);
	
    $logs = "";
    foreach($datas as $key => $value) {
        $logs .= $key . ":" . $value . " ";
    }
    //异步文件操作
    swoole_async_writefile(APP_PATH.'../runtime/log/'.date("Ym")
                           ."/".date("d")."_access.log"
                           , $logs.PHP_EOL
                           , function($filename){
        // todo
    }, FILE_APPEND);

}
public function onRequest($request, $response) {
    //防止访问资源损失性能
    if($request->server['request_uri'] == '/favicon.ico') {
        $response->status(404);
        $response->end();
        return ;
    }
```

elasticsearch也可以做日志系统

### 服务平滑启动 线上服务重启

使用信号源进行reload

```php
// sigterm sigusr1 usr2
/**
* @param $server
*/
public function onStart($server) {
    swoole_set_process_name("live_master");
}
```

```sh
echo "loading..."
pid=`pidof live_master`
echo $pid
kill -USR1 $pid #平滑重启
echo "loading success"
```

## 优化

