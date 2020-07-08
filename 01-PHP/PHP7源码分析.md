# PHP7底层源码

## PHP7的新特性

### PHP7性能对比

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
use Space\{ClassA, ClassB, ClassC as C};
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

该方法被添加为临时将对象范围绑定到闭包并调用它

早期使用bindTo — 复制当前闭包对象，绑定指定的$this对象和类作用域

```php
class Test {
	private $num = 1;
}
$f = function() {//闭包函数
	return $this-> num + 1;
};
echo $f->call(new Test);
```

#### 8.list 的方括号写法 

list把数组中的值赋给一组变量

```php
$arr = [1, 2, 3];
list($a, $b, $c) = $arr;

$arr = [1, 2, 3];
[$a, $b, $c] = $arr;//$a =1 $b=2 $c=3
```

#### 9.抽象语法树（AST）

![image-20200603153657066](https://i.loli.net/2020/06/03/EiOtZTKXC42f8ka.png)

```php
($a)['b'] = 1;
//array(1) { ["b"]=> int(1) }
```



## 基础变量

#### 1.zval的基本结构

- **zval** 定义 <u>可以表示任意类型</u> 大小是16字节

![image-20200603154726532](C:\Users\12605\AppData\Roaming\Typora\typora-user-images\image-20200603154726532.png)

- **Zend_value** 联合体 只能是一种类型 由u1里type决定

  ```c
  typedef union _zend_value {
  	zend_long         lval;				/* long value */
  	double            dval;				/* double value */
  	zend_refcounted  *counted;
  	zend_string      *str;
  	zend_array       *arr;
  	zend_object      *obj;		//对象
  	zend_resource    *res;		//资源类型
  	zend_reference   *ref;		//引用类型
  	zend_ast_ref     *ast;		//语法树
  	zval             *zv;
  	void             *ptr;		     	
  	zend_class_entry *ce; 		//类
  	zend_function    *func;		//函数
  	struct {
  		uint32_t w1;
  		uint32_t w2;
  	} ww;
  } zend_value;
  ```

- u1 联合体里type区分类型

  ```c
  union {
      struct {
          ZEND_ENDIAN_LOHI_3(
              zend_uchar    type,			//类型
              zend_uchar    type_flags,   //变量约束：常量
              union {
                  uint16_t  extra;        //not further specified 
              } u)
      } v;
      uint32_t type_info; //通过type_info取出值
  } u1;
  ```

  

  ```shell
  $ gdb /home/soft/php/bin/php
  (gbd) b ZEND_ECHO_SPEC_CV_HANDLER #b execute_ex  #断点
  (gbd) r zval.php #运行文件
  (gbd) n #下一个语句
  (gbd) c #下一个断点
  
  (gbd) p *z #打印
  ```

  

#### 字符串(zend_string)

- 定义

  ```c
  struct _zend_string {
  	zend_refcounted_h gc;		//垃圾回收
  	zend_ulong        h;        //用在数组 
  	size_t            len;		
  	char              val[1];	//跟val[1]表示一个字符串
  };
  ```

- 写实复制 ：a,b指向同一个字符串，a改变时会给新的空间

  ```c
  typedef struct _zend_refcounted_h {
  	uint32_t		refcount;			//给引用会加1 
  	union {
      	uint32_t		type_info;
  	} u;
  } zend_refcounted_h;
  
  struct _zend_refcounted {
  	zend_refcounted_h gc;
  };
  ```

  

#### 引用类型 (zend_reference)💎

- 定义

  ```c
  struct _zend_reference {
  	zend_refcounted_h              gc;
  	zval                           val;
  	zend_property_info_source_list sources;
  };
  ```

- 例子

  ```php
  $a = 'hello';
  $b = &$a; //此时$a,$b都为引用类型 指向同个ref 
  
  $b = 'world'; //都为引用 且指向同一个ref
  
  unset($b); //b为null a为引用 依然指向同一个ref
  ```

  

#### 数组类型 (zend_array/HashTable )

- 定义

  ```c
  typedef struct _zend_array HashTable; //另一个名字
  
  struct _zend_array {
  	zend_refcounted_h gc;
  	union {
  		struct {
  			ZEND_ENDIAN_LOHI_4(
  				zend_uchar    flags,
  				zend_uchar    _unused,
  				zend_uchar    nIteratorsCount,
  				zend_uchar    _unused2)
  		} v;
  		uint32_t flags;
  	} u;
  	uint32_t          nTableMask; //计算索引值
  	Bucket           *arData;
  	uint32_t          nNumUsed;   //已经使用的空间
  	uint32_t          nNumOfElements; //元素个数
  	uint32_t          nTableSize;	//默认为8 扩容x2
  	uint32_t          nInternalPointer;
  	zend_long         nNextFreeElement; 
  	dtor_func_t       pDestructor;
  };
  /*
  ```



```php
HashTable Data Layout

* =====================
  *
* +=============================+
* | HT_HASH(ht, ht->nTableMask) |
* | ...                         |
* | HT_HASH(ht, -1)             |
* +-----------------------------+
* ht->arData ---> | Bucket[0]   |
* | ...                         |
* | Bucket[ht->nTableSize-1]    |
* +=============================+
  */
* 理解HashTable 用CRUD
```

  ```php
  $a = [];  //初始化 nTableSize=8 nTableMask=-2
  
  $a[1] = "a";
  
  $a[] = 'b';
  
  $a["k1"] = "v1";
  $a["k2"] = "v2";
  
  echo $a["k1"];
  
  $a["k1"] = 'c';
  
  unset($a["k2"]);
  ```

  

## 内存管理

#### small/large/huge内存

#### 内存对齐

#### 内存类型标记



## 运行的生命周期

#### CLI模式

#### FPM模式

#### FastCGI协议

#### 网络编程/信号/多进程编程

## 编译与执行

#### 词法/语法分析基本原理

#### 抽象语法树( AST )

#### Zend虚拟机

#### 执行过程



## 基本语法实现的细节

## 扩展编写

