# PHP7底层源码

## PHP7的新特性

### PHP7性能基准测试,与PHP5性能的对比

​	php/Zend目录下有bench.php 

​	分别执行micro_bench.php和bench.php对比其性能

```shell
$ php Zend/bench.php
$ php Zend/micro bench.php
```

​	php7会比php5快很多👍

### PHP7中新的语法特性

#### 1.太空船操作符<=>🚀

- 太空船操作符用于**比较两个表达式**

- 例如，当$a小于、等于或大于$b时  它分别返回-1、0或1

  ```php
  echo 1<=>0;//1
  echo 1<=>1;//0
  echo 1<=>3;//-1
  ```

#### 2.类型声明🔒

- declare(strict_ types=1);       //       strict_ types= 1表示开启严格模式

  ```php
  function sumOfInts(int ...$ints)
  {
      return array_sum($ints);
  }
  
  var_dump(sumOfInts(2, '3', 4.1));//int(9)
  ```

#### 3.null合并操作符

```php
$page = isset($_GET['page']) ? $_GET['page']:0 ;

$page = $_GET['page']  ?? 0 ;
```

#### 4.常量数组 

不可修改

```php
define('ANIMALS', [ 'dog', 'cat', 'bird']);
```

#### 5.NameSpace批量导入📋

```php
use Space\\{ClassA, ClassB, ClassC as C};
```

#### 6.throwable接口❌ 可以捕获错误

```php
try {
	undefindfunc();
}catch (Error $e){
	var_dump($e);
}
#或者
set_exception_handLer(
function($e){
	var_dump($e) ;
});
undefindfunc() 
```

#### 7.Closure::call() 闭包call方法

```
class Test {
	private $num = 1;
}
$f = function() {//闭包函数
	return $this->num + 1;
};
echo $f->call(new Test);
```




## 基础变量

zval的基本结构

字符串(zend_string )

引用类型 (zend_reference )

数组类型 (zend_array/HashTable )



## 内存管理

small/large/huge内存
内存对齐
内存类型标记



## 运行的生命周期

CLI模式
FPM模式
FastCGI协议

网络编程/信号/多进程编程

## 编译与执行

词法/语法分析基本原理
抽象语法树( AST )
Zend虚拟机
执行过程



## 基本语法实现的细节

## 扩展编写

