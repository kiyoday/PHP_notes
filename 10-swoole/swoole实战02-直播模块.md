# 直播模块

## 业务分析

- 球队
- 人员
- 比分
- 直播员 图文直播
- 互联网用户

## 表设计

![image-20200630182755391](C:\Users\12605\Desktop\PHP_notes\.img\image-20200630182755391.png)

### 球队表

`null值`非常影响性能 所以`NOT NULL`给空字符串`''`

```mysql
CREATE  TABLE `live_team`(
  `id` tinyint(1) unsigned NOT NULL auto_increment,
  `name` VARCHAR(20) NOT NULL DEFAULT '', 
  `image` VARCHAR(20) NOT NULL DEFAULT '',
  `type` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `create_time` int(10) unsigned NOT NULL DEFAULT 0,
  `update_time` int(10) unsigned NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT charset=utf8;
```

### 直播表

```mysql
CREATE  TABLE `live_game`(
  `id` int(10) unsigned NOT NULL auto_increment,
  `a_id` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `b_id` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `a_score` int(10) unsigned NOT NULL DEFAULT 0,
  `b_score` int(10) unsigned NOT NULL DEFAULT 0,
  `narrator` VARCHAR(20) NOT NULL DEFAULT '',
  `image` VARCHAR(20) NOT NULL DEFAULT '',
  `start_time` int(10) unsigned NOT NULL DEFAULT 0,
  `status` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `create_time` int(10) unsigned NOT NULL DEFAULT 0,
  `update_time` int(10) unsigned NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT charset=utf8;
```

### 球员表

```mysql
CREATE  TABLE `live_player`(
  `id` int(10) unsigned NOT NULL auto_increment,
  `name` VARCHAR(20) NOT NULL DEFAULT '' COMMENT '球员名',
  `image` VARCHAR(20) NOT NULL DEFAULT '',
  `age` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `position` tinyint(1) unsigned NOT NULL DEFAULT 0 COMMENT '位置',
  `status` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `create_time` int(10) unsigned NOT NULL DEFAULT 0,
  `update_time` int(10) unsigned NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT charset=utf8;

```

### 赛事的赛况表

```mysql
CREATE  TABLE `live_outs`(
  `id` int(10) unsigned NOT NULL auto_increment,
  `game_id` int(10) unsigned NOT NULL DEFAULT 0,
  `team_id` tinyint(1) unsigned NOT NULL DEFAULT 0 COMMENT '队id',
  `content` VARCHAR(200) NOT NULL DEFAULT '',
  `image` VARCHAR(20) NOT NULL DEFAULT '',
  `type` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `status` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `create_time` int(10) unsigned NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT charset=utf8;
```

### 聊天室表

```mysql
CREATE  TABLE `live_chart`(
  `id` int(10) unsigned NOT NULL auto_increment,
  `game_id` int(10) unsigned NOT NULL DEFAULT 0,
  `user_id` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `content` VARCHAR(200) NOT NULL DEFAULT '',
  `status` tinyint(1) unsigned NOT NULL DEFAULT 0,
  `create_time` int(10) unsigned NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT charset=utf8;
```

## websocket 服务器

让客户端与服务器 创建一个长连接

传统的php实现使用ajax 定时询问服务器

```php
<?php
class Ws {
    CONST HOST = "0.0.0.0";
    CONST PORT = 8811;
    CONST CHART_PORT = 8812;

    public $ws = null;
    public function __construct() {
        // TODO redis 获取 key 有值 del
        $this->ws = new swoole_websocket_server(self::HOST, self::PORT);
        $this->ws->listen(self::HOST, self::CHART_PORT, SWOOLE_SOCK_TCP);

        $this->ws->set(
            [
                'enable_static_handler' => true,
                'document_root' => "/home/work/hdtocs/swoole_mooc/thinkphp/public/static",
                'worker_num' => 4,
                'task_worker_num' => 4,
            ]
        );

        $this->ws->on("start", [$this, 'onStart']);
        $this->ws->on("open", [$this, 'onOpen']);
        $this->ws->on("message", [$this, 'onMessage']);
        $this->ws->on("workerstart", [$this, 'onWorkerStart']);
        $this->ws->on("request", [$this, 'onRequest']);
        $this->ws->on("task", [$this, 'onTask']);
        $this->ws->on("finish", [$this, 'onFinish']);
        $this->ws->on("close", [$this, 'onClose']);

        $this->ws->start();
    }

    /**
     * @param $server
     */
    public function onStart($server) {
        swoole_set_process_name("live_master");
    }
    /**
     * @param $server
     * @param $worker_id
     */
    public function onWorkerStart($server,  $worker_id) {
        // 定义应用目录
        define('APP_PATH', __DIR__ . '/../../../application/');
        // 加载框架里面的文件
        //require __DIR__ . '/../thinkphp/base.php';
        require __DIR__ . '/../../../thinkphp/start.php';
    }

    /**
     * request回调
     * @param $request
     * @param $response
     */
    public function onRequest($request, $response) {
        if($request->server['request_uri'] == '/favicon.ico') {
            $response->status(404);
            $response->end();
            return ;
        }
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
        //上传文件
        $_FILES = [];
        if(isset($request->files)) {
            foreach($request->files as $k => $v) {
                $_FILES[$k] = $v;
            }
        }
        $_POST = [];
        if(isset($request->post)) {
            foreach($request->post as $k => $v) {
                $_POST[$k] = $v;
            }
        }

        $this->writeLog();
        $_POST['http_server'] = $this->ws;


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

        // 分发 task 任务机制，让不同的任务 走不同的逻辑
        $obj = new app\common\lib\task\Task;

        $method = $data['method'];
        $flag = $obj->$method($data['data'], $serv);
        /*$obj = new app\common\lib\ali\Sms();
        try {
            $response = $obj::sendSms($data['phone'], $data['code']);
        }catch (\Exception $e) {
            // todo
            echo $e->getMessage();
        }*/

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
     * 监听ws连接事件
     * @param $ws
     * @param $request
     */
    public function onOpen($ws, $request) {
        // fd redis [1] 插入在线用户 	向集合添加一个或多个成员
        \app\common\lib\redis\Predis::getInstance()
            ->sAdd(config('redis.live_game_key'), $request->fd);
        var_dump($request->fd);
    }

    /**
     * 监听ws消息事件
     * @param $ws
     * @param $frame
     */
    public function onMessage($ws, $frame) {
        echo "ser-push-message:{$frame->data}\n";
        $ws->push($frame->fd, "server-push:".date("Y-m-d H:i:s"));
    }

    /**
     * close
     * @param $ws
     * @param $fd
     */
    public function onClose($ws, $fd) {
        // fd del 删除 断开连接的在线用户
        \app\common\lib\redis\Predis::getInstance()
            ->sRem(config('redis.live_game_key'), $fd);
        echo "clientid:{$fd}\n";
    }

    /**
     * 记录日志
     */
    public function writeLog() {
        $datas = array_merge(['date' => date("Ymd H:i:s")],$_GET, $_POST, $_SERVER);

        $logs = "";
        foreach($datas as $key => $value) {
            $logs .= $key . ":" . $value . " ";
        }

        swoole_async_writefile(APP_PATH
                               .'../runtime/log/'.date("Ym")."/".date("d")
                               ."_access.log", $logs.PHP_EOL
                               , function($filename){
            // todo
        }, FILE_APPEND);

    }
}

new Ws();

// 20台机器    agent -> spark (计算) - 》 数据库   elasticsearch  hadoop

// sigterm sigusr1 usr2

```

### 流程图

![image-20200630204233853](C:\Users\12605\Desktop\PHP_notes\.img\image-20200630204233853.png)

## 直播图片上传、发布

### x-admin开源框架 搭建页面

### webuploader 上传文件 图片上传插件

```html
<!DOCTYPE html>
<html>
  
  <head>
    <meta charset="UTF-8">
    <title>singwa赛事直播-主持人页面</title>
    <meta name="renderer" content="webkit">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width,user-scalable=yes, minimum-scale=0.4, initial-scale=0.8,target-densitydpi=low-dpi" />
    <link rel="shortcut icon" href="/favicon.ico" type="image/x-icon" />
    <link rel="stylesheet" href="./css/font.css">
    <link rel="stylesheet" href="./css/xadmin.css">
    <link rel="stylesheet" type="text/css" href="../webuploader/webuploader.css">
    <script type="text/javascript" src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
    <script type="text/javascript" src="./lib/layui/layui.js" charset="utf-8"></script>
    <script type="text/javascript" src="./js/xadmin.js"></script>
    <!-- 让IE8/9支持媒体查询，从而兼容栅格 -->
    <!--[if lt IE 9]>
      <script src="https://cdn.staticfile.org/html5shiv/r29/html5.min.js"></script>
      <script src="https://cdn.staticfile.org/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
    <script type="text/javascript" src="../webuploader/webuploader.js"></script>
  
  </head>
  
  <body>
    <div class="x-body">
        <form class="layui-form">

          <div class="layui-form-item">
            <label for="username" class="layui-form-label">
              <span class="x-red">*</span>第几节
            </label>
            <div class="layui-input-inline">
              <select id="type" name="type" class="valid">
                <option value="1">第一节</option>
                <option value="2">第二节</option>
                <option value="3">第三节</option>
                <option value="4">第四节</option>
              </select>
            </div>
          </div>
          <div class="layui-form-item">
            <label for="username" class="layui-form-label">
              <span class="x-red">*</span>球队
            </label>
            <div class="layui-input-inline">
              <select id="team_id" name="team_id" class="valid">
                <option value="0">请选择</option>
                <option value="1">马刺</option>
                <option value="4">火箭</option>
              </select>
            </div>
          </div>


          <div class="layui-form-item layui-form-text">
              <label for="desc" class="layui-form-label">
                赛况内容
              </label>
              <div class="layui-input-block">
                  <textarea placeholder="请输入内容" id="content" name="content" class="layui-textarea"></textarea>
              </div>
          </div>

          <div class="layui-form-item layui-form-text">
            <label for="desc" class="layui-form-label">
              赛况图
            </label>
            <!--dom结构部分-->
            <div id="uploader-demo">
              <!--用来存放item-->
              <div id="fileList" class="uploader-list"></div>
              <div id="filePicker">选择图片</div>
            </div>

          </div>

          <div class="layui-form-item">
              <label for="L_repass" class="layui-form-label">
              </label>
              <button  type="submit" class="layui-btn"  lay-filter="add" id="submit-btn" lay-submit="">
                  增加
              </button>
          </div>
      </form>
    </div>
    <script>
      $list = $("#fileList");
      ratio = window.devicePixelRatio || 1,

      thumbnailWidth = 100 * ratio,
      thumbnailHeight = 100 * ratio,

      // Web Uploader瀹炰緥
      uploader;
      // 初始化Web Uploader
      var uploader = WebUploader.create({

        // 选完文件后，是否自动上传。
        auto: true,

        // swf文件路径
        swf:  'http://singwa.swoole.com:8811/webuploader/Uploader.swf',

        // 文件接收服务端。
        server: 'http://singwa.swoole.com:8811/?s=admin/image/index',

        // 选择文件的按钮。可选。
        // 内部根据当前运行是创建，可能是input元素，也可能是flash.
        pick: '#filePicker',

        // 只允许选择图片文件。
        accept: {
          title: 'Images',
          extensions: 'gif,jpg,jpeg,bmp,png',
          mimeTypes: 'image/*'
        }
      });

      // 当有文件添加进来的时候
      uploader.on( 'fileQueued', function( file ) {
        var $li = $(
                        '<div id="' + file.id + '" class="file-item thumbnail">' +
                        '<img>' +
                        '</div>'
                ),
                $img = $li.find('img');


        // $list为容器jQuery实例
        $list.append( $li );

        // 创建缩略图
        // 如果为非图片文件，可以不用调用此方法。
        // thumbnailWidth x thumbnailHeight 为 100 x 100
        uploader.makeThumb( file, function( error, src ) {
          if ( error ) {
            $img.replaceWith('<span>不能预览</span>');
            return;
          }

          $img.attr( 'src', src );
        }, thumbnailWidth, thumbnailHeight );
      });

      // 文件上传过程中创建进度条实时显示。
      uploader.on( 'uploadProgress', function( file, percentage ) {
        var $li = $( '#'+file.id ),
                $percent = $li.find('.progress span');

        // 避免重复创建
        if ( !$percent.length ) {
          $percent = $('<p class="progress"><span></span></p>')
                  .appendTo( $li )
                  .find('span');
        }

        $percent.css( 'width', percentage * 100 + '%' );
      });

      // 文件上传成功，给item添加成功class, 用样式标记上传成功。
      uploader.on( 'uploadSuccess', function( file, response) {
        if(response.status == 1) {
          $('#'+file.id).append('<input type="hidden" name="image" value="'+response.data.image+'"/>');
        }
        $( '#'+file.id ).addClass('upload-state-done');
      });

      // 文件上传失败，显示上传出错。
      uploader.on( 'uploadError', function( file ) {
        var $li = $( '#'+file.id ),
                $error = $li.find('div.error');

        // 避免重复创建
        if ( !$error.length ) {
          $error = $('<div class="error"></div>').appendTo( $li );
        }

        $error.text('上传失败');
      });

      // 完成上传完了，成功或者失败，先删除进度条。
      uploader.on( 'uploadComplete', function( file ) {
        $( '#'+file.id ).find('.progress').remove();
      });


        var $submitBtn = $('#submit-btn');
        // 提交表单
        $submitBtn.click(function (event) {
          event.preventDefault();
          var formData = $('form').serialize();
          // TODO: 请求后台接口跳转界面，前端跳转或者后台跳
          $.get("http://singwa.swoole.com:8811?s=admin/live/push&" + formData, function (data) {

            if (data.status == 1) {
                 alert(data.message);
            }
            // location.href='index.html';
          }, 'json');
        });



    </script>
    <script>var _hmt = _hmt || []; (function() {
        var hm = document.createElement("script");
        hm.src = "https://hm.baidu.com/hm.js?b393d153aeb26b46e9431fabaf0f6190";
        var s = document.getElementsByTagName("script")[0];
        s.parentNode.insertBefore(hm, s);
      })();</script>
  </body>

</html>
```

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200630205402169.png" alt="image-20200630205402169" style="zoom: 80%;float:left" />

```PHP
namespace app\admin\controller;
use app\common\lib\Util;

class Image
{
	
    public function index() {
        $file = request()->file('file');
        //保存文件
        $info = $file->move('../public/static/upload');
        if($info) {
            $data = [
                'image' => config('live.host')."/upload/".$info->getSaveName(),
            ];
            return Util::show(config('code.success'), 'OK', $data);
        }else {
            return Util::show(config('code.error'), 'error');
        }
    }
}
```



## 图文直播

数据流

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200630213437487.png" alt="image-20200630213437487" style="zoom:80%;float:left" />

```php
class Live
{
	//推送 实时图文数据 
    public function push() {
        if(empty($_GET)) {
            return Util::show(config('code.error'), 'error');
        }  // admin
        // TODO 验证token防止用户非法    
        // 加密md5(content)
        // TODO 加载数据 => mysql 获取
        $teams = [
            1 => [
                'name' => '马刺',
                'logo' => '/live/imgs/team1.png',
            ],
            4 => [
                'name' => '火箭',
                'logo' => '/live/imgs/team2.png',
            ],
        ];
		//组装数据
        $data = [
            'type' => intval($_GET['type']),
            
            'title' => !empty($teams[$_GET['team_id']]['name']) ?
            $teams[$_GET['team_id']]['name'] : '直播员',
            
            'logo' => !empty($teams[$_GET['team_id']]['logo']) ?
            $teams[$_GET['team_id']]['logo'] : '',
            
            'content' => !empty($_GET['content']) ? 
            $_GET['content'] : '',
            
            'image' => !empty($_GET['image']) ? $_GET['image'] : '',
        ];
        
        // 获取 连接的用户
        
        // TODO 赛况的基本信息入库MySQL   
        
        //2、数据组织好 push到直播页面 调用Task任务 pushLive
        $taskData = [
            'method' => 'pushLive',
            'data' => $data
        ];
        $_POST['http_server']->task($taskData);
        return Util::show(config('code.success'), 'ok');
    }
}
```

## 在线用户处理

`thinkphp\script\bin\server\ws.php`

```php
/**
* 监听ws连接事件
* @param $ws
* @param $request
*/
public function onOpen($ws, $request) {
    // fd redis [1] 插入在线用户 	向集合添加一个或多个成员
    \app\common\lib\redis\Predis::getInstance()
        ->sAdd(config('redis.live_game_key'), $request->fd);
    var_dump($request->fd);
}
/**
* close
* @param $ws
* @param $fd
*/
public function onClose($ws, $fd) {
    // fd del 删除 断开连接的在线用户
    \app\common\lib\redis\Predis::getInstance()
        ->sRem(config('redis.live_game_key'), $fd);
    echo "clientid:{$fd}\n";
}
```

### task任务 

通过task 机制发送赛况实时数据给客户端

```php
/**
* 通过task 机制发送赛况实时数据给客户端
* @param $data
* @param $serv swoole server对象
*/
public function pushLive($data, $serv) {
    $clients = Predis::getInstance()->sMembers(config("redis.live_game_key"));
	//每个客户端
    foreach($clients as $fd) {
        $serv->push($fd, json_encode($data));
    }
}
```