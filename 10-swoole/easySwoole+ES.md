

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

### 表设计

```mysql
CREATE TABLE `video` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(100) NOT NULL DEFAULT '',
  `cat_id` smallint(4) unsigned NOT NULL DEFAULT '0',
  `image` varchar(200) NOT NULL DEFAULT '',
  `url` varchar(200) NOT NULL DEFAULT '',
  `type` tinyint(1) unsigned NOT NULL DEFAULT '0',
  `content` text NOT NULL,
  `uploader` varchar(200) NOT NULL DEFAULT '',
  `create_time` int(10) unsigned NOT NULL DEFAULT '0',
  `update_time` int(10) unsigned NOT NULL DEFAULT '0',
  `status` tinyint(1) unsigned NOT NULL DEFAULT '0',
  `video_id` varchar(100) NOT NULL COMMENT '阿里云视频id',
  `video_duration` float(10,2) NOT NULL COMMENT '视频时长',
  PRIMARY KEY (`id`),
  KEY `name` (`name`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;
```



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

tar -zxvf video-project.tar

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



# 高性能API服务系统

## 阿里云-视频点播 VOD

'\\'和命名空间有关，因为原生的php没有用到命名空间，框架中启用了，会当前命名空间中寻找这个类，找不到会报错。

### 上传方法OSS封装

核心步骤的函数：

1. 使用AK初始化VOD客户端

   ```php
   class AliVod{
   
       public $regionId = 'cn-shanghai';
       public $client;
       public $ossClient;
   
       public function __construct() {
           $accessKey = Config::getInstance()->getConf("Aliyun");
           $profile = \DefaultProfile::getProfile($this->regionId
                                                  ,$accessKey['accessKeyId']
                                                  ,$accessKey['accessKeySecret']);
           $this->client =  new \DefaultAcsClient($profile);
       }
   ```

2. 获取视频上传地址和凭证

   ```php
   public function create_upload_video($title,$videoFileName,$other=[]) {
       $request = new vod\CreateUploadVideoRequest();
       $request->setTitle($title);        // 视频标题(必填参数)
       $request->setFileName($videoFileName); // 视频源文件名称，必须包含扩展名(必填参数)
       key_exists('description',$other)?$request->setDescription($other['description']): null;
       key_exists('coverURL',$other)?$request->setcoverURL($other['coverURL']): null;
       key_exists('tags',$other)?$request->setTags($other['tags']): null;
   
    
       $result = $this->client->getAcsResponse($request);
       if(empty($result)||empty($result->VideoId)){
           throw new \Exception("获取上传凭证不合法");
       }
       return $result;
   }
   ```

3. 使用上传凭证和地址初始化OSS客户端（注意需要先Base64解码并Json Decode再传入）

   ```php
   function initOssClient($uploadAuth, $uploadAddress) {
       $this->ossClient = new OssClient($uploadAuth['AccessKeyId']
               , $uploadAuth['AccessKeySecret']
               , $uploadAddress['Endpoint']
               , false, $uploadAuth['SecurityToken']);
       // 设置请求超时时间，单位秒，默认是5184000秒, 
       //建议不要设置太小，如果上传文件很大，消耗的时间会比较长
       $this->ossClient->setTimeout(86400*7);
       // 设置连接超时时间，单位秒，默认是10秒
       $this->ossClient->setConnectTimeout(10);  
       return $this->ossClient;
   }
   ```

4. 上传本地文件

   ```php
   function upload_local_file($uploadAddress, $localFile) {
       return $this->ossClient->uploadFile($uploadAddress['Bucket']
                                           , $uploadAddress['FileName']
                                           , $localFile);
   }
   ```

5. 获取上传后的视频信息

   ```php
   public function getPlayInfo($videoId = 0){
       if(empty($videoId)){
           return [];
       }
       $request =  new vod\GetPlayInfoRequest();
       $request->setVideoId($videoId);
       $request->setAcceptFormat("JSON");
   
       return $this->client->getAcsResponse($request);
   }
   ```

   

   调用返回信息

   ```php
   stdClass Object
   (
       [VideoBase] => stdClass Object
           (
               [Status] => Normal
               [VideoId] => b6d456f4119144db9844d771c04df7e3
               [TranscodeMode] => NoTranscode
               [CreationTime] => 2020-06-22T12:06:03Z
               [Title] => testVideo
               [MediaType] => video
               [CoverURL] => http://outin-978e549ab47211ea88e700163e1c7426.oss-cn-shanghai.aliyuncs.com/b6d456f4119144db9844d771c04df7e3/snapshots/8dc1c54aaeca4d63b03ab082843a39b3-00001.jpg?Expires=1592832447&OSSAccessKeyId=LTAIVVfYx6D0HeL2&Signature=Rh6M5BgQEpWqFFFiUz2E5ffBKT4%3D
               [Duration] => 14.614
               [OutputType] => oss
           )
   
       [RequestId] => 3A2358D3-983D-4324-A5AF-B08374690384
       [PlayInfoList] => stdClass Object
           (
               [PlayInfo] => Array
                   (
                       [0] => stdClass Object
                           (
                               [Status] => Normal
                               [StreamType] => video
                               [Size] => 613750
                               [Definition] => OD
                               [Fps] => 25
                               [Duration] => 14.614
                               [ModificationTime] => 2020-06-22T12:06:03Z
                               [Specification] => Original
                               [Bitrate] => 335.98
                               [Encrypt] => 0
                               [PreprocessStatus] => UnPreprocess
                               [Format] => mp4
                               [PlayURL] => https://outin-978e549ab47211ea88e700163e1c7426.oss-cn-shanghai.aliyuncs.com/sv/32a7450a-172dbebcdf6/32a7450a-172dbebcdf6.mp4?Expires=1592832447&OSSAccessKeyId=LTAIVVfYx6D0HeL2&Signature=P6qdDFxwZz75371LffeSw6JtjiA%3D
                               [NarrowBandType] => 0
                               [CreationTime] => 2020-06-22T12:06:03Z
                               [Height] => 360
                               [Width] => 640
                               [JobId] => b6d456f4119144db9844d771c04df7e302
                           )
   
                   )
   
           )
   
   )
   ```

## 对接本地上传和OSS

## 首页页面分页

使用mysqliDB提供的`paginate`方法

```php
class Video extends BaseModel
{
    public $tableName = "video";

    public function getVideoData($condition = [], $page = 1, $size = 10){
        if(!empty($size)){
            $this->db->pageLimit = $size;
        }

        $this->db->paginate($this->tableName,$page);
        echo $this->db->getLastQuery();
    }
}
```

#### 数据格式处理

原始方案一直接读取Mysq|

```php
public function getVideoData($condition = [], $page = 1, $size = 10){
    if (!empty($condition['cat_id'])){
        $this->db->where("cat_id",$condition['cat_id']);
    }
    //获取正常的内容
    $this->db->where("status",1);
    $this->db->pageLimit = $size;
    $this->db->orderBy('create_time','desc');
    $res = $this->db->paginate($this->tableName,$page);
    //调试：查看最后执行的SQL语句
    //echo $this->db->getLastQuery();
    $data = [
        'page_size' => $size,
        'count'=>$this->db->count,
        'total_page' => $this->db->totalPages,
        'lists' => $res,
    ];
    return $data;
}
```



## 视频首页高性能处理

- 原始方案一直接读取Mysq|

- 静态化API

  easyswoole + crontab
  easyswoole的定时器

- Redis缓存
- nginx + lua + easyswoole

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200623100702119.png" alt="image-20200623100702119" style="zoom:50%;float:left" />

## easyswoole定时任务

首先注册全局事件 staticApi

EasySwooleEvent.php中 `use EasySwoole\EasySwoole\Crontab\Crontab`;

```php
public static function mainServerCreate(EventRegister $register)
{
    // TODO: Implement mainServerCreate() method.
    /**
         * **************** Crontab任务计划 **********************
         */
    // 开始一个定时任务计划 
    Crontab::getInstance()->addTask(staticApi::class);
}
```

```php
<?php

namespace App;

use EasySwoole\EasySwoole\Crontab\AbstractCronTask;
use EasySwoole\EasySwoole\Task\TaskManager;
use App\Lib\Cache\Video as VideoCache;


class staticApi extends AbstractCronTask
{

    public static function getRule(): string
    {
        return '*/1 * * * *';
    }

    public static function getTaskName(): string
    {
        return  'staticApi';
    }

    function run(int $taskId, int $workerIndex)
    {
        $videoCacheObj = new VideoCache();

        TaskManager::getInstance()->async(function () use ($videoCacheObj){
           $videoCacheObj->setIndexVideo();
        });
    }

    function onException(\Throwable $throwable, int $taskId, int $workerIndex)
    {
        echo $throwable->getMessage();
    }
}

```

### 存储JSON文件

写入文件 setIndexVideo 

```php
<?php
namespace App\Lib\Cache;

use EasySwoole\EasySwoole\Config;
use App\Model\Video as videoModel;

class Video
{
    public function setIndexVideo(){
        $catIds = array_keys(Config::getInstance()->getconf('CATEGORY')) ;
        array_unshift($catIds,0);

        $videoModelObj = new videoModel();
        foreach ($catIds as $catId) {
            $condiction = [];
            if(!empty($catId)){
                $condiction['cat_id'] = $catId;
            }
            try {
                $data = $videoModelObj->getVideoCacheData($condiction,1);
            }catch (\Exception $e){
                $data = [];
            }

            if (empty($data)){
                continue;
            }
            foreach ($data as &$list) {
                $list['create_time'] = date("Ymd H:i:s",$list['create_time']);
                //tips: 转为合适时间格式 "%H:%M:%S"
                $list['video_duration'] = gmstrftime("%M:%S", $list['video_duration']);
            }

            //json形式写入文件当中
            //TODO 文件不存在新建文件夹
            $flag = file_put_contents(EASYSWOOLE_ROOT.
                                      "/webroot/video/json/{$catId}.json",json_encode($data));
            if(empty($flag)){
                //TODO 缓存失效报警
                echo "cat_id:{$catId} put data error".PHP_EOL;
            }else{
                echo "cat_id:{$catId} put data success".PHP_EOL;
            }
        }
    }
}
```

获取需要做缓存的mysql数据

```php
/**获取缓存数据
     * @param array $condition
     * @param int $page
     * @param int $size
     * @return array
     * @throws \Exception
     */
public function getVideoCacheData($condition = [], $page = 1, $size = 1000){
    if (!empty($condition['cat_id'])){
        $this->db->where("cat_id",$condition['cat_id']);
    }
    //获取正常的内容
    $this->db->where("status",1);
    $this->db->pageLimit = $size;
    $this->db->orderBy('create_time','desc');
    $res = $this->db->paginate($this->tableName,$page);
    //调试：查看最后执行的SQL语句
    //        echo $this->db->getLastQuery();
    return $res;
}
```

读取、使用缓存

```php
public function lists(){
    $catId = !empty($this->params['cat_id']) ? intval($this->params['cat_id']) : 0;
    try {
        $videoData = (new videoCache())->getCache($catId);
    }catch(\Exception $e) {
        return $this->writeJson(Status::CODE_BAD_REQUEST , "请求失败");
    }

    $count = count($videoData);
    return $this->writeJson(Status::CODE_OK,'OK',$videoData);
}
```

### array_splice 解决静态文件读取分页

```php
public function getParams() {
    $params = $this->request()->getRequestParam();
    $params['page'] = intval($params['page']??1);
    $params['size'] = intval($params['size']??5);

    $params['from'] = ($params['page'] - 1) * $params['size'];

    $this->params = $params;
}
//分页方法
public function getPagingData($count, $data){
    $totalPage = ceil($count/$this->params['size']);

    $data = $data??[];
    return [
        'total_page' => $totalPage,
        'page_size' => $this->params['page'],
        'count' => intval($count),
        'lists' => $data,
    ];
}
```

```php
public function lists(){
    $catId = !empty($this->params['cat_id']) ? intval($this->params['cat_id']) : 0;
    $videoFile = EASYSWOOLE_ROOT."/webroot/video/json/{$catId}.json";
    $videoData = is_file($videoFile)?json_decode(file_get_contents($videoFile)) :[];

    $count = count($videoData);
    //切割分页
    $videoData = array_splice($videoData
                              , $this->params['from']
                              , $this->params['size']);

    return $this->writeJson(Status::CODE_OK,'OK'
                            ,$this->getPagingData($count,$videoData));
}
```

### easyswoole毫秒定时器完美解决方案

使用方法

```php
// 每隔 2 秒执行一次
Timer::getInstance()->loop(2 * 1000, function () {
    echo "this timer runs at intervals of 2 seconds\n";
});

$register->add(EventRegister::onWorkerStart, function (
    $server , $workerId){
    if($workerId==0){
        Timer::getInstance()->loop(2 * 1000, function() use($workerId) {
            echo $workerId."\n";
        });
    }
});}
```

原来的定时任务改写

```php
$videoCacheObj = new VideoCache();

$register->add(EventRegister::onWorkerStart, function (
    $server , $workerId) use ($videoCacheObj) {
    if($workerId==0){
        Timer::getInstance()->loop(2 * 1000, function() use($videoCacheObj, $workerId) {
            $videoCacheObj->setIndexVideo();
        });
    }
 });}
```

## FastCache

EasySwoole 提供了一个快速缓存，是基础UnixSock通讯和自定义进程存储数据实现的，提供基本的缓存服务，本缓存为解决小型应用中，需要动不动就部署Redis服务而出现。

### 安装

```shell
$ composer require easyswoole/fast-cache
```

### 服务注册

我们在EasySwoole全局的事件中进行注册

```php
use EasySwoole\FastCache\Cache;
Cache::getInstance()->setTempDir(EASYSWOOLE_TEMP_DIR)->attachToServer(ServerManager::getInstance()->getSwooleServer());
```

FastCache只能在服务启动之后使用,需要有创建unix sock权限(建议使用vm,docker或者linux系统开发),虚拟机共享目录文件夹是无法创建unix sock监听的

### 客户端调用

服务启动后，可以在任意位置调用

```php
use EasySwoole\FastCache\Cache;
Cache::getInstance()->set('get','a');
var_dump(Cache::getInstance()->get('get'));
```

### Redis缓存

```php
$videoData = Di::getInstance()->get("REDIS")->get($this->getCatKey($catId));
```

## 封装三套静态缓存方案

### 设置缓存

```php
public function setIndexVideo(){
    $catIds = array_keys(Config::getInstance()->getconf('CATEGORY')) ;
    array_unshift($catIds,0);
    //获取缓存类型
    $cacheType = Config::getInstance()->getconf('INDEX_CACHE');

    $videoModelObj = new videoModel();

    // 写 json 缓存数据
    foreach ($catIds as $catId) {
        $condiction = [];
        if(!empty($catId)){
            $condiction['cat_id'] = $catId;
        }
        try {
            $data = $videoModelObj->getVideoCacheData($condiction,1);
        }catch (\Exception $e){
            $data = [];
        }

        if (empty($data)){
            continue;
        }
        foreach ($data as &$list) {
            $list['create_time'] = date("Ymd H:i:s",$list['create_time']);
            //tips: 转为合适时间格式 "%H:%M:%S"
            $list['video_duration'] = gmstrftime("%M:%S", $list['video_duration']);
        }

        //三套方案
        switch ($cacheType) {
            case 'file':
                //json形式写入文件当中  使用缓存实现
                $flag = file_put_contents($this->getVideoCatIdFile($catId), json_encode($data));
                break;
            case 'table':
                $flag = Cache::getInstance()->set($this->getCatKey($catId), json_encode($data));
                break;
            case 'redis':
                $res = Di::getInstance()->get("REDIS")->set($this->getCatKey($catId), json_encode($data));
                break;
            default:
                throw new \Exception("请求不合法");
                break;
        }

        if(empty($flag)){
            //TODO 缓存失效报警
            echo "cat_id:{$catId} put data error".PHP_EOL;
        }else{
            echo "cat_id:{$catId} put data success".PHP_EOL;
        }
    }
}
```

### 读取缓存

```php
public function getCache($catId = 0) {
    $cacheType = Config::getInstance()->getconf('INDEX_CACHE') ;

    switch ($cacheType) {
        case 'file':
            $videoFile = $this->getVideoCatIdFile($catId);
            $videoData = is_file($videoFile) ? file_get_contents($videoFile) : [];
            $videoData = !empty($videoData) ? json_decode($videoData, true) : [];
            break;

        case 'table':
            $videoData = Cache::getInstance()->get($this->getCatKey($catId));
            $videoData = !empty($videoData) ? json_decode($videoData, true) : [];
            break;
        case 'redis':
            $videoData = Di::getInstance()->get("REDIS")->get($this->getCatKey($catId));
            $videoData = !empty($videoData) ? json_decode($videoData, true) : [];
            break;
        default:
            throw new \Exception("请求不合法");
            break;
    }

    return $videoData;
}

public function getVideoCatIdFile($catId = 0) {
    return EASYSWOOLE_ROOT."/webroot/video/json/".$catId.".json";
}

public function getCatKey($catId = 0) {
    return "index_video_data_cat_id_".$catId;
}
```



### nginx + lua + easyswoole

## task异步任务实现播放量统计

在EasySwoole框架中，只需要配置好task配置项，即可在控制器，自定义进程内调用task实现异步任务:

```php
<?php
use EasySwoole\EasySwoole\Task\TaskManager;

class Index extends BaseController
{
    function index()
    {
        $task = TaskManager::getInstance();
        $task->async(function (){
            echo "异步调用task1\n";
        });
        $data =  $task->sync(function (){
            echo "同步调用task1\n";
            return "可以返回调用结果\n";
        });
        $this->response()->write($data);
    }
}
```

`zincrby:`

为`zset`有序集 `key` 的成员 `member` 的 `score` 值加上增量 `increment`

```php
public function zincrby($key, $number, $member){
    if(empty($key) || empty($member)){
        return false;
    }
    return $this->redis->zInCrBy($key, $number, $member);
}
```

调用，点击详情页面时

```php
// 播放数统计逻辑
// 投放task异步任务
$task = TaskManager::getInstance();
$task->async(function() use($id) {
    // 逻辑
    //sleep(10);
    // redis

    $res = Di::getInstance()->get("REDIS")
        ->zincrby(config::getInstance()->getConf("REDIS.video_play_key"), 1, $id);

    // 按天记录 按天记录 Redis Zunionstore 命令
});
```

`redis-cli`命令查看缓存情况

```shell
$ redis-cli
> ZREVRANGE video_play 0 -1 withscores
```

排行榜

```php
/** 获取排行榜信息 日 周 月 总排行
* @throws \Throwable
*/
public function rank(){
    $result = Di::getInstance()->get("REDIS")
        ->zRevRange(config::getInstance()->getConf("REDIS.video_play_key"),0,-1,true);
    return $this->writeJson(200, 'OK', $result);
}
```

## 用魔术方法调用底层方法

```php
/** 当类中不存在该方法时候，直接调用call实现调用底层redis相关的方法
* @param $name
* @param $arguments
*/
public function __call($name, $arguments)
{
    // TODO: Implement __call() method.
    // ... 为可变个数参数
    $this->redis->$name(...$arguments);
}
```



# 利用 ElacticSearch实现搜索服务

## 安装/安装插件elasticsearch-head

- `cd elasticsearch-head`
- `npm install`
- `npm run start`

`Gruntfile. js` 修改插件端口

`_site/app.js` 修改es链接地址

## 分布式部署

elasticsearch.yml 修改es配置

```yaml
http.host: 0.0.0.0
http.port: 9200

###################### 使用head等插件监控集群信息，需要打开以下配置项 ###########
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-credentials: true

# 集群名 集群名相同的节点会自动 组成集群

cluster.name: es

# 节点名称同理,可自动生成也可手动配置. 
node.name: es_node_1
node.master: true

```

其他主机

```yaml
node.name: es_node_2
node.master: false
# 这是一个集群中的主节点的初始列表,当节点(主节点或者数据节点)启动时使用这个列表进行探测
discovery.zen.ping.unicast.hosts: ["host1", "host2:port"]
```

## 索引

新建索引 

- 分片数：节点数的1.5~3倍

### 结构索引 新建索引 

使用复合查询创建索引 `http://localhost:9200/es/` 其中`es`表示索引名称

```json
{
  "mappings": {
    "video": {
      "properties": {
        "name": {
          "type": "text"
        },
        "cat_id": {
          "type": "integer"
        },
        "url": {
          "type": "text"
        },
        "image": {
          "type": "text"
        },
        "type": {
          "type": "byte"
        },
        "content": {
          "type": "text"
        },
        "uploader": {
          "type": "keyword"
        },
        "create_time": {
          "type": "integer"
        },
        "status": {
          "type": "byte"
        },
        "video_id": {
          "type": "keyword"
        }
      }
    }
  }
}
```

默认也是5分片1副本

![image-20200626114334408](C:\Users\12605\Desktop\PHP_notes\.img\image-20200626114334408.png)

可以用模板

## 文档的新增操作

`/索引名/文档名/文档id` 使用put 创建文档

post提交数据

```json
{"name":"名字2333","cat_id":"1","url":"http://sss","image":"http://baidu.com","type":"1","content":"dasdadadad","uploader":"kiyo","create_time":"1111111","status":"1","video_id":"sadffv111"}
```

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200626114813666.png" alt="image-20200626114813666" style="zoom:50%;float:left" />

成功信息

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200626114908549.png" alt="image-20200626114908549" style="zoom:50%;float:left" />

![image-20200626115012694](C:\Users\12605\Desktop\PHP_notes\.img\image-20200626115012694.png)

mysql数据同步到elasticsearch

## 文档的查询操作

中文在查询时需要复合查询

<img src="C:\Users\12605\Desktop\PHP_notes\.img\image-20200626122109267.png" alt="image-20200626122109267" style="zoom:50%;float:left" />

### aggs聚类

![image-20200626122521538](C:\Users\12605\Desktop\PHP_notes\.img\image-20200626122521538.png)

## 安装elasticsearch-php拓展

[文档](https://www.elastic.co/guide/cn/elasticsearch/php/current/_quickstart.html)

composer:  composer require

```json
"elasticsearch/elasticsearch": "~6.0"
```

查询

```php
public function es()
{
    $params = [
        "index" => "es",
        "type" => "video",
        'body' => [
            'query' => [
                'match' => [
                    'name' => '名字'
                ],
            ],
        ],
    ];
    $client = ClientBuilder::create()
        ->setHosts(["es01"])->build();

    $result = $client->search($params);
    $this->response()->write(json_encode($result));
}
```



## 封装成单例模式

封装到model

```php
<?php
namespace App\Model\Es;

use EasySwoole\Component\Singleton;
use EasySwoole\EasySwoole\Config;
use Elasticsearch\ClientBuilder;

class EsClient
{
    use Singleton;
    public $esClient = null ;

    private function __construct()
    {
        try{
            $config = Config::getInstance()->getConf("ELASTICSEARCH");
            $this->esClient = ClientBuilder::create()
                ->setHosts([$config['host']])->build();
        }catch (\Exception $e){
            throw new \Exception('elasticsearch 服务异常');
        }
        if (empty($this->esClient)){
            throw new \Exception('elasticsearch 连接失败');
        }
    }

    public function __call($name, $arguments)
    {
        // TODO: Implement __call() method.
        // ... 为可变个数参数
        return $this->esClient->$name(...$arguments);
    }
}
```

注册到全局`mainServerCreate()`

```php
Di::getInstance()->set('ES',EsClient::getInstance());
```

model分层

```php
class EsVideo extends EsBase
{
    public $index = 'es';
    public $type = 'video';

}

class EsBase {
	public $esClient = null;
	public function __construct() {
		$this->esClient = Di::getInstance()->get("ES");
	}

    /** 搜索
     * @param $name
     * @param int $from
     * @param int $size
     * @param string $type
     * @return array
     */
	public function searchByName($name, $from =0, $size = 10, $type = "match") {
		$name = trim($name);
		if(empty($name)) {
			return [];
		}
		$params = [
            "index" => $this->index,
            "type" => $this->type,
            'body' => [
                'query' => [
                    $type => [
                        'name' => $name
                    ], 
                ],
                'from' => $from,
                'size' => $size
            ],
        ];

        $result = $this->esClient->search($params);
        return $result;

	}
}
```



搜索api

```php
class Search extends BaseController
{

    public function index()
    {
        $keyword = trim($this->params['keyword']);
        if(empty($keyword)) {
            return $this->writeJson(Status::CODE_OK, "OK", $this->getPagingData(0, [], false));
        }

        $esObj = new EsVideo();
        $result = $esObj->searchByName($keyword,$this->params['from'], $this->params['size']);

        if(empty($result)) {
            return $this->writeJson(Status::CODE_OK, "OK", $this->getPagingData(0, [], false));
        }
        //命中数据
        $hits = $result['hits']['hits'];
        //命中个数
        $total = $result['hits']['total'];
        if(empty($total)) {
            return $this->writeJson(Status::CODE_OK, "OK", $this->getPagingData(0, [], false));
        }
        //格式化命中数据
        foreach($hits as $hit) {
            $source = $hit['_source'];
            $resData[] = [
                'id' => $hit['_id'],
                'name' => $source['name'],
                'image' => $source['image'],
                'uploader' => $source['uploader'],
                'create_time' => '', // TODO 在es中添加两个字段
                'video_duration' => '',
                'keywords' => [$keyword]
            ];
        }

        return $this->writeJson(Status::CODE_OK, "OK", $this->getPagingData($total, $resData, false));
    }
}
```



## 应用层大数据下搜索API逻辑开发优化方案以及IK分词器介绍

- es还有数据聚合 ，报表等功能

### 防止深度分页

```php
$totalPage = ceil($count/$this->params['size']);
$maxSize = Config::getInstance()->getConf('MAX_PAGE_SIZE');
//防止es深度分页 稳定性能
if( $totalPage > $maxSize ){
    $totalPage = $maxSize;
}
```

### 中文分词设置 IK分词器

首先，安装中文分词插件。这里使用的是 [ik](https://github.com/medcl/elasticsearch-analysis-ik/)，也可以考虑其他插件（比如 [smartcn](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-smartcn.html)）。

`$ ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.5.1/elasticsearch-analysis-ik-5.5.1.zip`

上面代码安装的是5.5.1版的插件，与 Elastic 5.5.1 配合使用。

接着，重新启动 Elastic，就会自动加载这个新安装的插件。

然后，新建一个 Index，指定需要分词的字段。这一步根据数据结构而异，下面的命令只针对本文。基本上，凡是需要搜索的中文字段，都要单独设置一下。

> ```bash
> $ curl -X PUT 'localhost:9200/accounts' -d '
> {
>   "mappings": {
>     "person": {
>       "properties": {
>         "user": {
>           "type": "text",
>           "analyzer": "ik_max_word",
>           "search_analyzer": "ik_max_word"
>         },
>         "title": {
>           "type": "text",
>           "analyzer": "ik_max_word",
>           "search_analyzer": "ik_max_word"
>         },
>         "desc": {
>           "type": "text",
>           "analyzer": "ik_max_word",
>           "search_analyzer": "ik_max_word"
>         }
>       }
>     }
>   }
> }
> ```

上面代码中，首先新建一个名称为`accounts`的 Index，里面有一个名称为`person`的 Type。`person`有三个字段。

> - user
> - title
> - desc

这三个字段都是中文，而且类型都是文本（text），所以需要指定中文分词器，不能使用默认的英文分词器。

Elastic 的分词器称为 [analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html)。我们对每个字段指定分词器。

> ```javascript
> "user": {
>   "type": "text",
>   "analyzer": "ik_max_word",
>   "search_analyzer": "ik_max_word"
> }
> ```

上面代码中，`analyzer`是字段文本的分词器，`search_analyzer`是搜索词的分词器。`ik_max_word`分词器是插件`ik`提供的，可以对文本进行最大数量的分词。



# 性能调优

## swoole升级

```bash
$ unzip swoole-src-master.zip
$ phpize
$ /configure --with-php-config=php配置文件路径
$ make && make install
```

## easyswoole升级

```bash
$ composer.phar require easyswoole/easyswoole=3.x-dev
$ easyswoole install
```

适配代码略。。。

## 全`协程`支持

### 协程

协程不是进程或线程，其执行过程更类似于`子例程`，或者说不带返回值的函数调用。
一个程序可以包含多个协程，可以对比与一个进程包含多个线程，因而下面我们来比较`协程`和`线程`。

我们知道多个线程相对独立，有自己的上下文，切换`受系统控制`；

而协程也相对独立，有自己的上下文，但是其切换`由自己控制`，由当前协程切换到其他协程由当前协程来控制。 <img src="C:\Users\12605\Desktop\PHP_notes\.img\Coroutine.png" alt="协程" style="zoom:67%;" />

- 安装mysqli类库

    ```bash
    $ composer require easyswoole/mysqli 
    ```

## 连接池

> 连接池是创建和管理一个连接的缓冲池的技术，这些连接准备好被任何需要它们的线程使用。

简单来说，就是创建一个`容器`，并且把`资源提前准备好`放在里面，比如我们常用的`redis连接`、`mysql连接`。

如果我们先把连接连接好，并且放在连接池中，程序中需要使用就从池中获取，执行操作。

就省去了反复`创建`连接、`断开`连接的操作。

可以减少`I/O`操作，提高资源利用率。



- 安装通用连接池

  ```bash
  $ composer require easyswoole/pool
  ```

  



## Nginx负载均衡

## 分布式

## Linux性能调优



# 总结



