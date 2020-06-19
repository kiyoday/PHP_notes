## 面向对象

类：现实世界或思维世界中的实体在计算机中的反映，它将数据以及这些数据上的操作封装在一起。比如人类、动物类

对象: 具有类类型的变量。比如人类中的张三这就是一个对象。他有人类的特性，比如说话写字等。

属性：类型的特征 比如人的名字、年龄、身高都是人的特征属性。

方法：方法是将一些操作封装起来的过程。类似函数。

继承：从父类那获得特征。比如人从父亲那继承了父亲的姓名。

### class (类)

每个类的定义都以关键字 *class* 开头，后面跟着类名，后面跟着一对花括号，里面包含有类的属性与方法的定义

类名是非php保留字以外的合法的标签。合法的类名和变量一样。字母、下划线开头，后面跟任意字母或数字。

自 PHP 5.5 起，关键词 *class* 也可用于类名的解析。使用 *ClassName::class* 你可以获取一个字符串

```php
class Person {
  public $name;
  public $age;
  
  public function say(){
    echo "hello";
  }
}
echo Person::class;//Person 如果带命名空间。则会加上命名空间

```

要创建一个对象。使用new关键字

```php
$zhang = new Person();
$zhang->name  = '张三';
```

### extends （继承）

一个类可以在声明中用 *extends* 关键字继承另一个类的方法和属性。PHP不支持多重继承，一个类只能继承一个基类。

被继承的方法和属性可以通过用同样的名字重新声明被覆盖。如果父类的方法使用了final。则不会被覆盖。

当覆盖方法时，参数必须保持一致否则 PHP 将发出 **E_STRICT** 级别的错误信息。但构造函数例外，构造函数可在被覆盖时使用不同的参数

```php
class Boy extends Person {
  
}
```

### 类常量

在类中始终保持不变的值定义为常量。在定义和使用常量的时候不需要使用 $ 符号

类常量是一个定值。类常量使用const 定义。访问的时候使用**self::**访问类常量

```php
class C{
  const PI = 3.1415926;
  public function test(){
    echo self::PI;
  }
}
```

### 自动加载

使用自动加载可以让我们不用一个个去include 类文件

自动加载不可用于 PHP 的 CLI

```php
spl_autoload_register(function($class){
  require $class.".php";
})
```

### 构造函数、析构函数

构造函数在对象创建的时候，会被调用。适合初始化工作。

如果子类中定义了构造函数则不会隐式调用其父类的构造函数。要执行父类的构造函数，需要在子类的构造函数中调用**parent::__construct()**

析构函数在到某个对象的所有引用都被删除或者当对象被显式销毁时执行

试图在析构函数（在脚本终止时被调用）中抛出一个异常会导致致命错误。

```php
class P{
  public function __construct(){
    echo "construct";
  }
  public function __destruct(){
    echo "destruct";
  }
}

$p = new P();// construct;
unset($p);//destruct;


```

### 访问控制

对属性或方法的访问控制，是通过在前面添加关键字 *public*（公有），*protected*（受保护）或 *private*（私有）来实现的。

public 修饰的 可以在任何地方访问到

protected修饰的只能在子类或该类中访问

private修饰的只能在该类中访问。

```php
class A{
  public $name = 'a';
  protected $age = 10;
  private $money = 100;
}
class B extends A{
  public function test(){
    echo $this->age;//a

  }
  public function testPrivate(){
  	echo $this->money;
  }
}
$b = new B();
echo $b->name;//a
echo $b->test();//10
# 不可访问
echo $b->age;//error;
#子类不能访问
echo $b->testPrivate();//error
```

### 范围解析操作符（::）

范围解析操作符（也可称作 Paamayim Nekudotayim）或者更简单地说是一对冒号，可以用于访问静态成员，类常量。还可以用于覆盖类中的属性和方法。

self，parent 和 static 这三个特殊的关键字是用于在类定义的内部对其属性或方法进行访问的

当一个子类覆盖其父类中的方法时，PHP 不会调用父类中已被覆盖的方法。是否调用父类的方法取决于子类。使用self调用父类，使用$this 调用本类。

```php
class A{
  public $name = 'a';
  protected $age = 10;
  private $money = 100;
}
class B extends A{
  public static $s = 's';
  const PI = 111;
  public function test(){
    echo $this->age;// 10

  }
  
  public static function testStatic(){
    echo self::$s;
  } 
  public function testConst(){
    echo self::PI;
  }
  public function testPrivate(){
  	echo $this->money;
  }
}
# self 和 $this
class ParentClass {
    function test() {
        self::who();    // will output 'parent'
        $this->who();    // will output 'child'
    }

    function who() {
        echo 'parent';
    }
}

class ChildClass extends ParentClass {
    function who() {
        echo 'child';
    }
}

$obj = new ChildClass();
$obj->test();//
```

### static 静态关键字

声明类属性或方法为静态，就可以不实例化类而直接访问。静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）

静态属性不可以由对象通过 -> 操作符来访问。静态属性只能被初始化为文字或常量。静态属性不随着对象的销毁而销毁。

```php
class P{
  $a = "world";
  public static function test(){
    echo "hello".self::$a;
  }
}
p::test();
```

### 抽象类

从具体事物抽出、概括出它们共同的方面、本质属性与关系等，而将个别的、非本质的方面、属性与关系舍弃，这种思维过程，称为抽象。

php中如果一个类的方法被定义为抽象，那么该类就是抽象类。抽象类不能被实例化。只能被子类继承，子类必须实现全部的抽象方法，并且访问修饰必须和父类相同或者更宽松。参数必须一致。

```php
abstract class AbstractClass {
  abstract public function test();
}

class Son extends AbstractClass{
  public function test(){
	echo "test";
  }
}
```

### 对象接口

接口泛指实体把自己提供给外界的一种[抽象化]（可以为另一实体），用以由内部操作分离出外部沟通方法，使其能被内部修改而不影响外界其他实体与其交互的方式。

使用接口不需要实现哪些方法，只需要定义这些方法。具体的实现由类去实现。

接口通过**interface**定义。实现接口通过**implements**。接口可以被继承

接口中也可以定义常量。但是不能定义属性。接口允许多继承

```php
interface Iter{
  public function test();
}

class ClassT implements Iter{
  public function test(){
    
  }
}
interface Iter2 extends Iter {
  public function test2();
}

class ClassT2 implements Iter2 {
  public function test(){}
  public function test2(){}
}

#多继承
interface Iter3 extends Iter1,Iter2{}

/**
* An example of duck typing in PHP
*/

interface CanFly {
  public function fly();
}

interface CanSwim {
  public function swim();
}

class Bird {
  public function info() {
    echo "I am a {$this->name}\n";
    echo "I am an bird\n";
  }
}

/**
* some implementations of birds
*/
class Dove extends Bird implements CanFly {
  var $name = "Dove";
  public function fly() {
    echo "I fly\n";
  } 
}

class Penguin extends Bird implements CanSwim {
  var $name = "Penguin";
  public function swim() {
    echo "I swim\n";
  } 
}

class Duck extends Bird implements CanFly, CanSwim {
  var $name = "Duck";
  public function fly() {
    echo "I fly\n";
  }
  public function swim() {
    echo "I swim\n";
  }
}

/**
* a simple function to describe a bird
*/
function describe($bird) {
  if ($bird instanceof Bird) {
    $bird->info();
    if ($bird instanceof CanFly) {
      $bird->fly();
    }
    if ($bird instanceof CanSwim) {
      $bird->swim();
    }
  } else {
    die("This is not a bird. I cannot describe it.");
  }
}

// describe these birds please
describe(new Penguin);
echo "---\n";

describe(new Dove);
echo "---\n";

describe(new Duck);
```

### Trait

Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制.无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合

#### trait定义

```php
trait t{
  public function test(){}
}
# 使用
class Test{
  use t;
  public function test2{
    $this->test();
  }
}
```

#### 优先级

从基类继承的成员会被 trait 插入的成员所覆盖。优先顺序是来自当前类的成员覆盖了 trait 的方法，而 trait 则覆盖了被继承的方法。

```php
class Base {
	public function say(){
		echo "base";
	}
}
trait Test{
	public function say(){
		echo "trait";
	}
}

class Son extends Base {
	use Test;
	public function test(){
		$this->say();
	}
}

$s = new Son();
$s->test();//trait
# 在子类中重写say。则调用子类的say方法 
public function say(){
		echo "son";
}
```

#### 多个trait组合

通过逗号分隔，在 use 声明列出多个 trait，可以都插入到一个类中

```php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World';
    }
}

class MyHelloWorld {
    use Hello, World;
    public function sayExclamationMark() {
        echo '!';
    }
}
```

#### 冲突的解决

如果多个trait中。都有同名的方法，则会产生冲突，冲突会产生一个致命的错误。

为了解决多个 trait 在同一个类中的命名冲突，需要使用 *insteadof* 操作符来明确指定使用冲突方法中的哪一个

*as* 操作符可以 为某个方法引入别名

```php
trait A {
    public function smallTalk() {
        echo 'a';
    }
    public function bigTalk() {
        echo 'A';
    }
}

trait B {
    public function smallTalk() {
        echo 'b';
    }
    public function bigTalk() {
        echo 'B';
    }
}

class Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
    }
}

class Aliased_Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
        B::bigTalk as talk;
    }
}
```

#### 使用as修改访问控制

```php
class Base {
	public function say(){
		echo "base";
	}
}
trait Test{
	public function say(){
		echo "trait";
	}
}

class Son extends Base {
	use Test {say as private say2;}
	public function say(){
		echo "son";
	}
}

$s = new Son();
$s->say2();//error
```



#### 使用多个trait组合

```php
trait A{}
trait B{}
trait C{use A,B;}
```

#### trait抽象成员方法

为了对使用的类施加强制要求，trait 支持抽象方法的使用

```php
trait T{
  abstract public function test();
}

class Test{
  use T;
  public function test(){}
}
```

#### trait 静态方法

```php
trait T{
   public static function test() {};
}

class Test{
  use T;
}
Test::test();
```

####  trait定义属性

Trait 定义了一个属性后，类就不能定义同样名称的属性

```php
trait PropertiesTrait {
    public $same = true;
    public $different = false;
}

class PropertiesExample {
    use PropertiesTrait;
    public $same = true; // PHP 7.0.0 后没问题，之前版本是 E_STRICT 提醒
    public $different = true; // 致命错误

```

### 匿名类

**PHP 7** 开始支持匿名类。 匿名类很有用，可以创建一次性的简单对象

匿名类被嵌套进普通 Class 后，不能访问这个外部类（Outer class）的 private（私有）、protected（受保护）方法或者属性。 为了访问外部类（Outer class）protected 属性或方法，匿名类可以 extend（扩展）此外部类。 为了使用外部类（Outer class）的 private 属性，必须通过构造器传进来.

```php
$a = new class {
  public function test(){
    echo "test";
  }
};
$a->test();
```

### 重写、重载

override是重写（覆盖）了一个方法，以实现不同的功能。一般是用于子类在继承父类时，重写（重新实现）父类中的方法。

**重写（覆盖）的规则**：
   1、重写方法的参数列表必须完全与被重写的方法的相同,否则不能称其为重写而是重载.
   2、重写方法的访问修饰符一定要大于被重写方法的访问修饰符（public>protected>default>private）。
   3、重写的方法的返回值必须和被重写的方法的返回一致；
   4、重写的方法所抛出的异常必须和被重写方法的所抛出的异常一致，或者是其子类；
   5、被重写的方法不能为**private**，否则在其子类中只是新定义了一个方法，并没有对其进行重写

   6、静态方法不能被重写为非静态的方法（会编译出错）。

overload是重载，一般是用于在一个类内实现若干重载的方法，这些方法的名称相同而参数形式不同。

**重载的规则**：
   1、在使用重载时只能通过相同的方法名、不同的参数形式实现。不同的参数类型可以是不同的参数类型，不同的参数个数，不同的参数顺序（参数类型必须不一样）；
   2、不能通过访问权限、返回类型、抛出的异常进行重载；
   3、方法的异常类型和数目不会对重载造成影响；

PHP所提供的"重载"（overloading）是指动态地"创建"类属性和方法。我们是通过魔术方法（magic methods）来实现的。

```php
class A{
  public function test(){
    echo "11";
  }
}

class B extends A{
  #重写父类的方法
  public function test(){
    echo "22";
  }
}

class C{
  public function __call($name,$args) {
    echo $name;
  }
  public function __callStatic($name, $arguments){}
}
```

### 多态

多态：同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果。在运行时，可以通过指向基类的指针，来调用实现派生类中的方法。

```php
abstract class animal{
    abstract function fun();
}
class cat extends animal{
    function fun(){
        echo "cat say miaomiao...";
    }
}
class dog extends animal{
    function fun(){
        echo "dog say wangwang...";
    }
}
function work($obj){
    if($obj instanceof animal){
        $obj -> fun();
    }else{
        echo "no function";
    }
}
work(new dog()); 
work(new cat());
```

### 遍历对象

遍历对象可以使用foreach遍历可见属性。或者实现iterator接口

```php
class MyClass
{
    public $var1 = 'value 1';
    public $var2 = 'value 2';
    public $var3 = 'value 3';

    protected $protected = 'protected var';
    private   $private   = 'private var';

}
$c = new MyClass();

foreach ($c as $key=>$v) {
  echo $key."=>"$v;
}

```

### 魔术方法

- __construct 初始化调用
- __desturct 对象销毁时调用
- __call 访问一个不存在的方法的时候调用
- __callStatic 访问一个不存在的静态方法调用
- __get() 访问一个不存在的属性调用
- __set() 修改一个不存在的属性调用
- __isset() 使用isset判断一个高属性的时候调用
- __toString() 当一个对象以一个字符串返回时候触发调用
- __invoke()当把一个对象当初函数去调用的时候 触发



### final

final 最终的，如果一个类被定位成final 这个类不能被继承。如果一个方法被定义一个final。这个方法不能被覆盖。

final不能修饰属性。

```php
class A{
	final public function test(){}
}

Class B extends A{
  public function test(){ //error
    
  }
}
```

### 对象复制、对象比较

对象复制可以通过 **clone** 关键字来完成

当对象被复制后，PHP 5 会对对象的所有属性执行一个浅复制（shallow copy）。所有的引用属性 仍然会是一个指向原来的变量的引用。

```php
class A{

	public $name = "hello";
}

$a  = new A();

$b = clone $a;
```

当使用比较运算符（*==*）比较两个对象变量时，比较的原则是：如果两个对象的属性和属性值 都相等，而且两个对象是同一个类的实例，那么这两个对象变量相等。

而如果使用全等运算符（*===*），这两个对象变量一定要指向某个类的同一个实例（即同一个对象）

### 类型约束

函数的参数可以指定必须为对象（在函数原型里面指定类的名字），接口，数组

```php
function Test(A $a){}
```

### 后期静态绑定

“后期绑定”的意思是说，*static::* 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。也可以称之为“静态绑定”，因为它可以用于（但不限于）静态方法的调用

**self的限制**

使用 *self::* 或者 *__CLASS__* 对当前类的静态引用，取决于定义当前方法所在的类

```php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}//

B::test();//A
#静态绑定语法
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        static::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}//

B::test();//B
## 实现ar model
class Model 
{ 
    public static function find() 
    { 
        echo static::$name; 
    } 
} 

class Product extends Model 
{ 
    protected static $name = 'Product'; 
} 

Product::find();
```

### 对象和引用

> PHP 的引用是别名，就是两个不同的变量名字指向相同的内容。在 PHP 5，一个对象变量已经不再保存整个对象的值。只是保存一个标识符来访问真正的对象内容。 当对象作为参数传递，作为结果返回，或者赋值给另外一个变量，另外一个变量跟原来的不是引用的关系，只是他们都保存着同一个标识符的拷贝，这个标识符指向同一个对象的真正内容。

```php
class A {
    public $foo = 1;
}  

$a = new A;
$b = $a;     // $a ,$b都是同一个标识符的拷贝
             // ($a) = ($b) = <id>
$b->foo = 2;
echo $a->foo."\n";
```

### 对象序列化

在会话中存储对象。使用serialize序列化。

## 命名空间

命名空间简单的说就是类似文件的路径。为了解决命名冲突的问题产生的。比如在我们的代码中经常遇到以下问题。

自己在一个目录写了一个类叫A. 另外一个人也写了一个类叫A。就会产生一个冲突。最初的框架，比如ci框架，都是类的名字前面加上一CI_的前缀防止命名冲突。

命令空间的思想来源于路径。比如在一个/www/目录下不能存在两个相同的文件。但是可以文件放在/www/a/ 、/www/b下面，这样两个文件就不会冲突。同理命名空间。如果在\www\a\A、\www\b\A 这两个类也可以可以同时存在的。

命名空间的定义使用**namespace** 定义。只有类（包括抽象类和traits）、接口、函数和常量受到命名空间的影响。

命名空间的定义必须在脚本的首行。不能有bom，否则会出错。

定义子命名空间用\分割。

也可以在一个文件定义多个命名空间。但是一般不这么做。

```php
namespace app;
#子命名空间
namespace app\model;


namespace app {
  function test(){}
}
namespace app2{
  function test(){}
}
namespace {
  app\test();
  app2\test();
}

```

###　命名空间基础

命名空间和路径原理相似。所以有相对、绝对

- 非限定名称、不包含前缀的类名称。类似a.php 比如include('a.php')

  ```php
  $a = new Test();// 如果这个文件定义的命名空间是app。那么就是访问的 app\Test类。
  ```

- 限定名称，或包含前缀 类似 文件路径中的 a/c.php这种形式

  ```php
  $a = new model\Test();　//如果该文件的命名空间是app。则访问的就是app\model\Test
  ```

- 完全限制 类似文件中的绝对路径  /www/a.php

  ```php
  $a = new \app\Test();
  ```

### 别名和导入

命名空间支持别名和引入外部的名字

所有支持命名空间的PHP版本支持三种别名或导入方式：为类名称使用别名、为接口使用别名或为命名空间名称使用别名



```php
namespace a;
use app\model;
use app\test\model as model2;

```

### 全局命名空间

如果没有定义任何命名空间，所有的类与函数的定义都是在全局空间，与 PHP 引入命名空间概念前一样。在名称前加上前缀 *\\* 表示该名称是全局空间中的名称

```php
namespace app;

function test(){
  new \Redis();
}
```



### 名词解析

```php
namespace A;
use B\D, C\E as F;

// 函数调用

foo();      // 首先尝试调用定义在命名空间"A"中的函数foo()
            // 再尝试调用全局函数 "foo"

\foo();     // 调用全局空间函数 "foo" 

my\foo();   // 调用定义在命名空间"A\my"中函数 "foo" 

F();        // 首先尝试调用定义在命名空间"A"中的函数 "F" 
            // 再尝试调用全局函数 "F"

// 类引用

new B();    // 创建命名空间 "A" 中定义的类 "B" 的一个对象
            // 如果未找到，则尝试自动装载类 "A\B"

new D();    // 使用导入规则，创建命名空间 "B" 中定义的类 "D" 的一个对象
            // 如果未找到，则尝试自动装载类 "B\D"

new F();    // 使用导入规则，创建命名空间 "C" 中定义的类 "E" 的一个对象
            // 如果未找到，则尝试自动装载类 "C\E"

new \B();   // 创建定义在全局空间中的类 "B" 的一个对象
            // 如果未发现，则尝试自动装载类 "B"

new \D();   // 创建定义在全局空间中的类 "D" 的一个对象
            // 如果未发现，则尝试自动装载类 "D"

new \F();   // 创建定义在全局空间中的类 "F" 的一个对象
            // 如果未发现，则尝试自动装载类 "F"

// 调用另一个命名空间中的静态方法或命名空间函数

B\foo();    // 调用命名空间 "A\B" 中函数 "foo"

B::foo();   // 调用命名空间 "A" 中定义的类 "B" 的 "foo" 方法
            // 如果未找到类 "A\B" ，则尝试自动装载类 "A\B"

D::foo();   // 使用导入规则，调用命名空间 "B" 中定义的类 "D" 的 "foo" 方法
            // 如果类 "B\D" 未找到，则尝试自动装载类 "B\D"

\B\foo();   // 调用命名空间 "B" 中的函数 "foo" 

\B::foo();  // 调用全局空间中的类 "B" 的 "foo" 方法
            // 如果类 "B" 未找到，则尝试自动装载类 "B"

// 当前命名空间中的静态方法或函数

A\B::foo();   // 调用命名空间 "A\A" 中定义的类 "B" 的 "foo" 方法
              // 如果类 "A\A\B" 未找到，则尝试自动装载类 "A\A\B"

\A\B::foo();  // 调用命名空间 "A\B" 中定义的类 "B" 的 "foo" 方法
              // 如果类 "A\B" 未找到，则尝试自动装载类 "A\B"
```

## 错误和异常

在编码的时候，我们无时无刻会遇到错误和异常，所以我们需要处理这些错误。

php的错误类型有

- E_ERROR 致命的错误。会中断程序的执行
- E_WARNING 警告。不会中断程序
- E_NOTICE 通知，运行时通知。表示脚本遇到可能会表现为错误的情况
- E_PARSE 解析错误，一般是语法错误。
- E_STRICT   PHP 对代码的修改建议
- E_DEPRECATED 将会对在未来版本中可能无法正常工作的代码给出警告

### 错误

在开发模式中，我们一般需要打开**error_reporting** 设置为**E_ALL**。然后把**display_errors** 设置为on

如果需要记录错误日志，则需要配置log_errors.

开发者可以 通过set_error_handle()自己接管错误。

```php
set_error_handle(function($errno,$errstr,$errfile,$errline){
  
})
  
/**
* throw exceptions based on E_* error types
*/
set_error_handler(function ($err_severity, $err_msg, $err_file, $err_line, array $err_context)
{
    // error was suppressed with the @-operator
    if (0 === error_reporting()) { return false;}
    switch($err_severity)
    {
        case E_ERROR:               throw new ErrorException            ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_WARNING:             throw new WarningException          ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_PARSE:               throw new ParseException            ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_NOTICE:              throw new NoticeException           ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_CORE_ERROR:          throw new CoreErrorException        ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_CORE_WARNING:        throw new CoreWarningException      ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_COMPILE_ERROR:       throw new CompileErrorException     ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_COMPILE_WARNING:     throw new CoreWarningException      ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_USER_ERROR:          throw new UserErrorException        ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_USER_WARNING:        throw new UserWarningException      ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_USER_NOTICE:         throw new UserNoticeException       ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_STRICT:              throw new StrictException           ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_RECOVERABLE_ERROR:   throw new RecoverableErrorException ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_DEPRECATED:          throw new DeprecatedException       ($err_msg, 0, $err_severity, $err_file, $err_line);
        case E_USER_DEPRECATED:     throw new UserDeprecatedException   ($err_msg, 0, $err_severity, $err_file, $err_line);
    }
});

class WarningException              extends ErrorException {}
class ParseException                extends ErrorException {}
class NoticeException               extends ErrorException {}
class CoreErrorException            extends ErrorException {}
class CoreWarningException          extends ErrorException {}
class CompileErrorException         extends ErrorException {}
class CompileWarningException       extends ErrorException {}
class UserErrorException            extends ErrorException {}
class UserWarningException          extends ErrorException {}
class UserNoticeException           extends ErrorException {}
class StrictException               extends ErrorException {}
class RecoverableErrorException     extends ErrorException {}
class DeprecatedException           extends ErrorException {}
class UserDeprecatedException       extends ErrorException {}
```

#### PHP7的错误处理

PHP 7 改变了大多数错误的报告方式。error可以通过exception异常进行捕获到.不能通过try catch捕获。但是可以通过注册到set_exception_handle捕获。

- Throwable
  - Error
  - Exception

```php
try
{
   // Code that may throw an Exception or Error.
}
catch (Throwable $t)
{
   // Executed only in PHP 7, will not match in PHP 5
}
catch (Exception $e)
{
   // Executed only in PHP 5, will not be reached in PHP 7
}
```

### 异常

捕获异常可以通过try catch 语句

```php
try{
  //异常的代码
}catch (Exception $e){
  //处理异常
}finally{
  //最后执行的
}
```

