

# 概述

## 项目概述

### EasySwoole技术点

- http server
- 多进程异步任务消息队列
- 定时器连接池协程
- 全局事件注册
- 框架底层源码分析

### ElasticSearch技术点

- ElasticSearch + easyswoole结合
- ElasticSearch调优

### 业务技术点

- 拦截器
- 应对高并发大数据场景
- 全局错误处理
- 静态缓存redis缓存引入
- 代码高度复用
- Nginx + lua + easyswoole
- 高性能yaconf服务引入
- 前后端分离VUE + EasySwoole

### 业务架构

<img src="C:\Users\12605\Desktop\PHP_notes\.img\gnEXmAfOT9kxNbZ.png" alt="image-20200618154947317" style="zoom:100%;float:left;" />

# EasySwoole框架

## EasySwoole简介

适用于编写写底层的API

底层是基于Swoole开发的一个常驻内存型的分布式PHP框架

```shell
$ docker rm $(docker ps -aq)
#适用power shell
$ docker cp be6398f2a85f:/easyswoole ./cp#复制文件以后再挂载
$ docker run -ti -p 9501:9501 --name es -v $pwd/es/easyswoole:/easyswoole:rw es

$ php easyswoole start
```

### 基本使用

复制`App\HttpController\index.php`控制器

新建`App\HttpController\Category.php`

```php
class Category extends Controller
{

    public function index()
    {
        $data = Array(
            'id'=> 1,
            'name'=>'kiyo'
        );
        //用该方法返回json数据
        $this->writeJson(200,$data,'这个是成功消息');
    }
}
```

http://localhost:9501/category/index 可以访问控制器下的index方法 

### API规范

新建目录`App\HttpController\Api`

>  注意Category的命名空间 App\HttpController\Api

此时可用http://localhost:9501/api/category/index 访问





在category下定义video方法，使用`request`实例接收请求参数

```php
public function video()
{
    $data = Array(
        'id'=> 1,
        'name'=>'kiyo',
        'params'=>$this->request()->getRequestParam(),
    );
    $this->writeJson(200,$data,'这个是成功消息');
}
```

### 一些easyswoole的命令

```bash
#-d后台重启  --p-8888设定端口
$ php easyswoole -d --p-8888 restart
```

实现是在`vendor/bin/easyswoole`下源码实现

### 抽象必须实现index的解决方法

原`Controller文件`定义index为抽象方法，因此之类继承必须定义

做个base类即可，以后可以放公用方法

```php
class BaseController extends controller
```

### 抛出指定异常

重载Controller父类的`onException`

```php
protected function onException(\Throwable $throwable): void
{
	$this->writeJson(400,'请求错误');
}
```

### EasySwoole结合Mysql使用

- DB库安装

- ```shell
  $ composer require easyswoole/mysqli
  $ composer require joshcam/mysqli-database-class:dev-master
  ```

- 检查PHP7是否有Mysqli扩展模块

  ```php
  $ php -m 
  #在docker中添加mysqli
  ```

- 基本使用

  ```php
  $config = new \EasySwoole\Mysqli\Config([
      'host'          => 'db',
      'port'          => 3306,
      'user'          => 'root',
      'password'      => 'root',
      'database'      => 'es',
      'timeout'       => 5,
      'charset'       => 'utf8',
  ]);
  
  $client = new \EasySwoole\Mysqli\Client($config);
  
  go(function ()use($client){
      //构建sql
      $client->queryBuilder()->get('user_list');
      //执行sql
      var_dump($client->execBuilder());
  });
  ```

  `EasySwooleEvent.php`文件下可以用`mainServerCreate`直接注册一个DI的单例模式

  ```php
  public static function mainServerCreate(EventRegister $register){
          // mysql 配置 小伙伴 放到 ini文件
          // 之前es2 - mysql 需要小伙伴 用老师讲解的协程链接池 去优化
          Di::getInstance()->set('MYSQL',\MysqliDb::class,Array
          	(
              'host' => 'db',
              'username' => 'root',   
              'password' => 'root',
              'db'=> 'es',
              'port' => 3306,
              'charset' => 'utf8')    
          );
  ```

  `\vendor\easyswoole\component\src\Di.php`

  ```php
  class Di
  {	//使用单例模式
      use Singleton;
      private $container = array();
  ```

  trait 是单例模式

  ```php
  //Singleton 文件
  trait Singleton
  {
      private static $instance;
  ```

- 使用Di得到mysql单例

  ```php
  $db = Di::getInstance()->get("MYSQL");
  $result = $db->where("id", 1)->getOne("video");
  return $this->writeJson(200, 'OK', $result);
  ```

  

# 性能测试

Ab工具的的安装

```shell
$ yum -y install httpd-tools
```

使用

```shell
$ ab -n 1000 -c 100
```

性能指标

```shell
Server Software:        EasySwoole
Server Hostname:        localhost
Server Port:            9501

Document Path:          /
Document Length:        26 bytes

Concurrency Level:      100
Time taken for tests:   0.363 seconds
Complete requests:      1000
Failed requests:        0
Non-2xx responses:      1000 
Total transferred:      173000 bytes
HTML transferred:       26000 bytes
Requests per second:    2754.94 [#/sec] (mean) #QPS平均每秒请求
Time per request:       36.298 [ms] (mean)
Time per request:       0.363 [ms] (mean, across all concurrent requests)
Transfer rate:          465.43 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    8   4.5      8      14
Processing:     3   20  24.3     13     103
Waiting:        2   18  24.3     10     100
Total:          7   28  23.0     23     106

Percentage of the requests served within a certain time (ms)
  50%     23
  66%     24
  75%     25
  80%     26
  90%     52
  95%    100
  98%    102
  99%    105
 100%    106 (longest request)
```



# 玩转高性能消息队列服务



## 消息队列解释

![image-20200619130939806](C:\Users\12605\Desktop\PHP_notes\.img\image-20200619130939806.png)

常用消息队列

- kafka 常用做日志队列
- rabbitMQ
- redis的list

## 安装并封装Redis

安装

```shell
$ composer require easyswoole/redis
```

使用

```php
$redis = new Redis(new RedisConfig([
    'host' => 'redis',
    'port' => '6379',
    'serialize' => RedisConfig::SERIALIZE_NONE
]));
$redis->set("key",'value');
$result = $redis->get('key');
return $this->writeJson(200, 'OK', $result);
```

### 封装

```php
namespace App\lib\Redis;
use EasySwoole\Component\Singleton;
use EasySwoole\Redis\Config\RedisConfig;

class Redis
{
    use Singleton;

    public $redis='';

    private function __construct()
    {
        if (!extension_loaded('redis')){
            throw new \Exception('redis 扩展无法加载');
        }
        try{
            $this->redis = new \EasySwoole\Redis\Redis(new RedisConfig([
                'host' => 'redis',
                'port' => '6379',
                'serialize' => RedisConfig::SERIALIZE_NONE
            ]));
        }catch (\Exception $e){
            throw new \Exception('redis 服务异常');
        }
    }
    
    public function get($key){
        if(empty($key)){
            return '';
        }
        return $this->redis->get($key);
    }
}
```

```php
$result = Redis::getInstance()->get('key');
return $this->writeJson(200, 'OK', $result);
```

- 封装到es\easyswoole\EasySwooleEvent.php

```php
Di::getInstance()->set('REDIS',Redis::getInstance());
```

调用

```php
$redis = Di::getInstance()->get("REDIS");
$result = $redis->get('key');
return $this->writeJson(200, 'OK', $result);
```

## 独立配置文件

根目录下dev.php 追加：

```php
'REDIS'=>[
    'host' => 'redis',
    'port' => '6379',
    'time_out' => 3,
],
```

redis.php

```php
$config = Config::getInstance()->getConf("REDIS");
$this->redis = new \EasySwoole\Redis\Redis(
    new RedisConfig($config)
);
```

## 高性能配置文件服务安装yaconf

# 前后端分离架构

## 安装Node和npm

```shell
docker run -ti -p 8088:8088 --name nodedev node /bin/bash

node -v
npm -v

tar -xvf video-project.tar

npm install #安装依赖

npm run dev 
```

上线后部署

```shell
npm run build

cd dist
cp html /webroot
```

配置nginx代理静态文件:视频

```nginx
location / {
	root easyswoole/webroot ;
	index  index. html index. htm;
	if (!-e $request_filename){
		proxy_pass http://es:8000;
	}
}
```



## 上传界面接口联调

views/Upload.vue 文件中进行接口联调

1. 视频上传接口
   请看views/Upload.vue 文件的第7，8行代码
   action="/api/upload/file"即后台接口地址
   name="video"既为 file 的字段名字
   accept="video/mp4" 视频文件被限制为只能是 mp4的形式
2. 图片上传接口联调
   请看文件components/ImgUpload.vue文件
   action="/api/upload/file"接口地址
   name="image" 字段名称
   accept="image/jpeg, image/png, image/jpg" 可以上传的文件类型控制，前端自己使用
3. 表单提交接口
   请看views/Upload.vue 文件的第109行代码
   this.axios.post('/admin/video/add'....

## 功能架构

![image-20200620190157541](C:\Users\12605\Desktop\PHP_notes\.img\image-20200620190157541.png)

- 普通架构

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200620190558614.png" alt="image-20200620190558614" style="zoom: 80%;float:left" />

- 高可用架构

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200620190710799.png" alt="image-20200620190710799" style="zoom:80%;float:left" />

# 小视频业务

## 小视频业务介绍

- 点播形式
- 小 1-5分钟

### 小视频处理

​	视频上传->源视频存储->转码分发->加速播放

​	满足多终端多清晰度播放

​	小型公司最好接入第三方视频处理SDK

### 	开发流程

​	需求评审-> UE图设计->接口定义->接口开发->前后端联调

​	使用postman上传视频、图片文件

![image-20200620204043003](C:\Users\12605\Desktop\PHP_notes\.img\image-20200620204043003.png)

## 视频上传到本地

使用Request里的方法

```php
$video = $request->getUploadedFile("file");
$flag = $video->moveTo("/easyswoole/webroot/1.mp4");
$data = [
    'url'=>"1.mp4",
    'flag'=>$flag
];
if($flag){
    return $this->writeJson(200,'OK',$data);
}else{
    return $this->writeJson(400,'OK',$data);
}
```

封装到lib中

```php
<?php


namespace App\Lib\Upload;

use App\Lib\Utils;
use EasySwoole\Http\Request;

class Base
{
    //上传文件的 file - key
    public $type = '';
    private $ClientMediaType;

    public function __construct(Request $request)
    {
        $this->request = $request;
        $files = $this->request->getSwooleRequest()->files;
        $types = array_keys($files);
        $this->type = $types[0];

    }

    public function upload(){
        if($this->type != $this->fileType){
            return false;
        }

        $videos = $this->request->getUploadedFile($this->type);
        $this->size = $videos->getSize();
        $this->checkSize();

        $fileName = $videos->getClientFileName();
        //video/mp4形式
        $this->ClientMediaType = $videos->getClientMediaType();
        $this->checkMediaType();//拆分检查

        $file = $this->getFile($fileName);
        $result = $videos->moveTo($file);
        if(!empty($result)){
            return $this->file;
        }
        return false;
    }

    public function getFile($fileName){
        $pathinfo = pathinfo($fileName);
        $extentsion = $pathinfo['extension'];
        //  /video/2020/06
        $dirname = '/'.$this->type.'/'.date("Y").'/'.date("m");
        //  /easyswoole/webroot/video/2020/06
        $dir = EASYSWOOLE_ROOT.'/webroot'.$dirname;
        if(!is_dir($dir)){
            mkdir($dir,0777,true);
        }
        //  /9c5a2c47376e21ae.mp4
        $basename = '/'.Utils::getFileKey($fileName).'.'.$extentsion;
        //返回给前端的  /video/2020/06/9c5a2c47376e21ae.mp4
        $this->file = $dirname.$basename;
        //  保存用的绝对路径
        //  /easyswoole/webroot/video/2020/06/9c5a2c47376e21ae.mp4
        $realpath = $dir.$basename;
        return $realpath;
    }
    
    public function checkMediaType(){
        $clientMediaType = explode("/", $this->ClientMediaType);
        $clientMediaType = $clientMediaType[1] ?? "";
        if(empty($clientMediaType)||
            !in_array($clientMediaType,$this->fileExtTypes)){
            throw new \Exception("上传{$this->type}文件不合法");
        }
    }

    public function checkSize(){
        if(empty($this->size)){
            return false;
        }
    }
}

class Video extends Base
{
    public $fileType = 'video';

    public $maxSize = 122;

    public $fileExtTypes = [
        'mp4',
        'x-flv',
    ];
}
//控制器
class Upload extends BaseController
{
    public function file(){
        $request = $this->request();
        try{
            $obj = new Video($request);
            $file = $obj->upload();
        }catch (\Exception $e){
            return $this->writeJson(400,$e->getMessage(),[]);
        }
        if(empty($file)){
            return $this->writeJson(400,"上传失败",[]);
        }
        $data = [
            'url' => $file,
        ];
        return $this->writeJson(200,"OK",$data);
    }
}
```

## 图片上传封装

### 方法一：判断类型

```php
$request = $this->request; 
$files = $request->getSwooleRequest()->files;
$types = array_keys($files);
$type = $types[0];
if($type == 'image'){
    $obj = new Image($request);
    $file = $obj->upload();
}
```

### 方法二：利用反射机制

```php
class ClassArr {

    //定义类对应命名空间
	public function uploadClassStat() {
		return [
			"image" => "\App\Lib\Upload\Image",
			"video" => "\App\Lib\Upload\Video",
		];
	}
    //获得类名
	public function initClass($type, $supportedClass, $params = [], $needInstance = true) {
		if(!array_key_exists($type, $supportedClass)) {
			return false;
		}

		$className = $supportedClass[$type];

		return $needInstance ? (new \ReflectionClass($className))->newInstanceArgs($params) : $className;
	}

}
```

```php
$request = $this->request();
$files = $request->getSwooleRequest()->files;
$types = array_keys($files);
$type = $types[0];
try{
    $classObj = new ClassArr();
    $classStats = $classObj->uploadClassStat();
    $uploadObj = $classObj->initClass($type,$classStats,[$request,$type]);
    $file = $uploadObj->upload();
}
```



## 验证规则

安装validate包

`composer require easyswoole/validate`

controller下添加validate

```php
protected function validate(Validate $validate)
{
    return $validate->validate($this->request()->getRequestParam());
}
```

调用校验规则

```php
$Validate = new Validate();
$Validate->addColumn('name', 'name错误')
    ->required('名字不为空')->lengthMin(10,'最小长度不小于10位');

$ret = $this->validate($Validate);

//返回错误信息
if($ret == false){
    $this->writeJson(Status::CODE_BAD_REQUEST
                     ,$Validate->getError()->getFieldAlias()
                     ,$Validate->getError()->getErrorRuleMsg()
                    );
}
```



# 打造高性能API服务系统一easyswoole API篇





# 利用easyswoole + ES打造高性能小视频搜索服务



# 性能调优



# 总结



