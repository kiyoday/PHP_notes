## PHP高级函数

### 一、自定义函数

#### 1.普通函数
```php
function name($value=''){
	代码段；
}
```

- 一个函数只完成一个功能
- 不调用不执行
- 碰到return语句再将控制权移交到调用函数的位置

```php
function 函数名称(函数参数='默认值'){
	代码段；
	return 返回值
}
```

- 函数名不区分大小写
- 不支持聪明
- 最好遵循驼峰法

#### 2.值传递与引用传递

- &为引用符号

- 引用传递给的是地址

  ```php
  function change(&$str){
  	$str .= '?';
  	return $str;
  }
  ```

### 二、特殊形式函数

#### 1.可变函数

在PHP中如果将"**函数名称**"赋予**字符串**类型的变量🙄

但是在使用该变量时，如果带有小括号,那么PHP引擎将解析函数

```php
$funcName = 'strtolower';
$a = $funcName('KIYO');//相当于strtolower()
echo $a; //kiyo
print_r(get_defined_functions());//
```



#### 2.回调函数

回调函数就是调用函数的时候将另外一个函数**<font color =red>当作参数</font>**传递进去，并且在函数体中进行调用

1. ##### 通过可变函数调用😥

   ```php
   function study(){
       echo 'studying......';
   }
   
   function doWhat($funcName){
       //通过可变函数的形式进行调用
       $funcName();
   }
   doWhat('study');
   ```

- 被调需要参数的形式，回调函数也要加参数

  ```php
  function play($user1, $user2){
      echo $user1.' and '.$user2.' are playing.......';
  }
  
  function doWhat($funcName, $param1,$param2){
      //通过可变函数的形式进行调用
      $funcName($param1,$param2);
  }
  doWhat('play','Pi','Mo');
  ```

- [array_map()](https://www.php.net/manual/zh/function.array-map.php)函数 ($callback,  $array) 对数组中每个元素应用回调函数的操作，不会改原函数

  ```php
  function addTo2($num){
      return $num+=2;
  }
  
  $arr = [1,2,3,4];
  var_dump(array_map('addTo',$arr));//3456
  print_r($arr);//1234
  ```

- array_walk()函数($array,  $callback) 回调处理 会改变原本数组

  ```php
  function addTo2(&$num){ // 注意加入引用
      return $num+=2;
  }
  
  $arr = [1,2,3,4];
  array_walk($arr, 'addTo2') //true 返回值为bool
  var_dump($arr);//3456
  ```

- array_filter ($array,  $callback) 过滤出新数组

  ```php
  function odd($num){//过滤出奇数
      if($num%2==1){
          return $num;
      }
  }
  
  $arr = [1,2,3,4];
  var_dump(array_filter($arr,'odd'));
  print_r($arr);
  ```

 ##### 2.可以通过call_user_func()和call_user_func_array()

-  call_user_func($callback , $param1)  

  ```php
  function play($user1, $user2){
      echo $user1.' and '.$user2.' are playing.......';
  }
  call_user_func('play','Pi','Mo');//调用
  ```

- call_user_func_array()

  ```php
  call_user_func_array('play',['Pi','Mo']);//仅传入参数类型不同
  ```

  

#### 3.匿名函数（闭包函数）

允许临时创建一个**<u>没有指定名称</u>**😶的函数，最经常用作回调函数参数的值。

闭包函数也可以作为**<u>变量的值</u>**来使用🙃

1. ##### 创建闭包函数

    ```php
    $func = function(){
        return 'this is a function';
    };
    echo $func();//this is a function
    ```

- 带参数

    ```php
    $withParam = function ($param){
        return $param;
    };
    echo $withParam('这个是参数');//这个是参数
    ```

2. ##### 使用array_map

    ```php
    array(function($var){return $var*2;},$array)
    ```

#### 4.引用文件✨

- ###### require/require_once 

  ​	包含文件不存在会产生一个致命错误和一个警告，程序**终止执行**



- ###### include/include_once 

  ​	包含文件不存在会产生两个警告，程序**继续执行**

