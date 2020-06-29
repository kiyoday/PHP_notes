## 设计模式分类

开始实验之前，有必要先了解一些背景信息和相关基础知识。

> 在[软件工程](https://zh.wikipedia.org/wiki/軟體工程)中，**设计模式**（design pattern）是对[软件设计](https://zh.wikipedia.org/wiki/軟件設計)中普遍存在（反复出现）的各种问题，所提出的解决方案。
>
> 设计模式并不直接用来完成[代码](https://zh.wikipedia.org/wiki/程式碼)的编写，而是描述在各种不同情况下，要怎么解决问题的一种方案。[面向对象](https://zh.wikipedia.org/wiki/面向对象)设计模式通常以[类别](https://zh.wikipedia.org/wiki/類別)或[对象](https://zh.wikipedia.org/wiki/物件_(電腦科學))来描述其中的关系和相互作用，但不涉及用来完成应用程序的特定类别或对象。设计模式能使不稳定依赖于相对稳定、具体依赖于相对抽象，避免会引起麻烦的紧耦合，以增强软件设计面对并适应变化的能力。
>
> 《[设计模式](https://zh.wikipedia.org/wiki/设计范例)》一书原先把设计模式分为创建型模式、结构型模式、行为型模式，把它们通过授权、聚合、诊断的概念来描述
>
>  --参考维基百科

### 设计模式的类型

根据设计模式的参考书 **Design Patterns - Elements of Reusable Object-Oriented Software（中文译名：设计模式 - 可复用的面向对象软件元素）** 中所提到的，总共有 23 种设计模式。这些模式可以分为三大类：创建型模式（Creational Patterns）、结构型模式（Structural Patterns）、行为型模式（Behavioral Patterns）

| 序号 | 模式 & 描述                                                  | 包括                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1    | **创建型模式** 这些设计模式提供了一种在创建对象的同时隐藏创建逻辑的方式，而不是使用` new 运算符`直接实例化对象。这使得程序在判断针对某个给定实例需要创建哪些对象时更加灵活。 | 工厂模式（Factory Pattern）抽象工厂模式（Abstract Factory Pattern）单例模式（Singleton Pattern）建造者模式（Builder Pattern）原型模式（Prototype Pattern） |
| 2    | **结构型模式** 这些设计模式关注类和对象的组合。继承的概念被用来组合接口和定义组合对象获得新功能的方式。 | 适配器模式（Adapter Pattern）桥接模式（Bridge Pattern）过滤器模式（Filter、Criteria Pattern）组合模式（Composite Pattern）装饰器模式（Decorator Pattern）外观模式（Facade Pattern）享元模式（Flyweight Pattern）代理模式（Proxy Pattern） |
| 3    | **行为型模式** 这些设计模式特别关注对象之间的通信。          | 责任链模式（Chain of Responsibility Pattern）命令模式（Command Pattern）解释器模式（Interpreter Pattern）迭代器模式（Iterator Pattern）中介者模式（Mediator Pattern）备忘录模式（Memento Pattern）观察者模式（Observer Pattern）状态模式（State Pattern）空对象模式（Null Object Pattern）策略模式（Strategy Pattern）模板模式（Template Pattern）访问者模式（Visitor Pattern） |

### 设计模式的六大原则

**1、开闭原则（Open Close Principle）**

开闭原则的意思是：**对扩展开放，对修改关闭**。在程序需要进行拓展的时候，不能去修改原有的代码，实现一个热插拔的效果。简言之，是为了使程序的扩展性好，易于维护和升级。想要达到这样的效果，我们需要使用接口和抽象类,

> 实现热插拔，提高扩展性。 

**2、里氏代换原则（Liskov Substitution Principle）**

里氏代换原则是面向对象设计的基本原则之一。 里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。LSP 是继承复用的基石，只有当派生类可以替换掉基类，且软件单位的功能不受到影响时，基类才能真正被复用，而派生类也能够在基类的基础上增加新的行为。里氏代换原则是对开闭原则的补充。实现开闭原则的关键步骤就是抽象化，而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。

> 实现抽象的规范，实现子父类互相替换； 

**3、依赖倒转原则（Dependence Inversion Principle）**

这个原则是开闭原则的基础，具体内容：针对接口编程，依赖于抽象而不依赖于具体。

> 针对接口编程，实现开闭原则的基础； 

**4、接口隔离原则（Interface Segregation Principle）**

这个原则的意思是：使用多个隔离的接口，比使用单个接口要好。它还有另外一个意思是：降低类之间的耦合度。由此可见，其实设计模式就是从大型软件架构出发、便于升级和维护的软件设计思想，它强调降低依赖，降低耦合。

> 降低耦合度，接口单独设计，互相隔离； 

**5、迪米特法则，又称最少知道原则（Demeter Principle）**

最少知道原则是指：一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立。

> 功能模块尽量独立 

**6、合成复用原则（Composite Reuse Principle）**

合成复用原则是指：尽量使用合成/聚合的方式，而不是使用继承。

> 尽量使用聚合，组合，而不是继承； 

### 具体分类

设计模式主要分为三大类，各自还有许多子类：

- 创建型模式

| 模式名       | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| 抽象工厂模式 | 为一个产品族提供了统一的创建接口。当需要这个产品族的某一系列的时候，可以从抽象工厂中选出相应的系列创建一个具体的工厂类。 |
| 工厂方法模式 | 定义一个接口用于创建对象，但是让子类决定初始化哪个类。工厂方法把一个类的初始化下放到子类。 |
| 生成器模式   | 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。 |
| 惰性初始模式 | 推迟对象的创建、数据的计算等需要耗费较多资源的操作，只有在第一次访问的时候才执行。 |
| 对象池模式   | 通过回收利用对象避免获取和释放资源所需的昂贵成本。           |
| 原型模式     | 用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。 |
| **单例模式** | 确保一个类只有一个实例，并提供对该实例的**全局访问**。       |

- 结构性模式

| 模式名     | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 适配器模式 | 将某个类的接口转换成客户端期望的另一个接口表示。适配器模式可以消除由于接口不匹配所造成的类兼容性问题。 |
| 桥接模式   | 将一个抽象与实现解耦，以便两者可以独立的变化。               |
| 组合模式   | 把多个对象组成树状结构来表示局部与整体，这样用户可以一样的对待单个对象和对象的组合。 |
| 修饰模式   | 向某个对象动态地添加更多的功能。修饰模式是除类继承外另一种扩展功能的方法。 |
| 外观模式   | 为子系统中的一组接口提供一个一致的界面， 外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。 |
| 享元       | 通过共享以便有效的支持大量小颗粒对象。                       |
| 代理       | 为其他对象提供一个代理以控制对这个对象的访问。               |

- 行为型模式

| 模式名     | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 黑板       | 广义的观察者在系统范围内交流信息，允许多位读者和写者。       |
| 责任链     | 为解除请求的发送者和接收者之间耦合，而使多个对象都有机会处理这个请求。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它。 |
| 命令       | 将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可取消的操作。 |
| 解释器     | 给定一个语言, 定义它的文法的一种表示，并定义一个解释器, 该解释器使用该表示来解释语言中的句子。 |
| 迭代器     | 提供一种方法顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示。 |
| 中介者     | 包装了一系列对象相互作用的方式，使得这些对象不必相互明显作用，从而使它们可以松散偶合。当某些对象之间的作用发生改变时，不会立即影响其他的一些对象之间的作用，保证这些作用可以彼此独立的变化。 |
| 备忘录     | 备忘录对象是一个用来存储另外一个对象内部状态的快照的对象。备忘录模式的用意是在不破坏封装的条件下，将一个对象的状态捉住，并外部化，存储起来，从而可以在将来合适的时候把这个对象还原到存储起来的状态。 |
| 空对象     | 通过提供默认对象来避免空引用。                               |
| 观察者模式 | 在对象间定义一个一对多的联系性，由此当一个对象改变了状态，所有其他相关的对象会被通知并且自动刷新。 |
| 规格       | 以布尔形式表示的可重绑定的商业逻辑。                         |
| 状态       | 让一个对象在其内部状态改变的时候，其行为也随之改变。状态模式需要对每一个系统可能获取的状态创立一个状态类的子类。当系统的状态变化时，系统便改变所选的子类。 |
| 策略       | 定义一个算法的系列，将其各个分装，并且使他们有交互性。策略模式使得算法在用户使用的时候能独立的改变。 |
| 模板方法   | 模板方法模式准备一个抽象类，将部分逻辑以具体方法及具体构造子类的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。不同的子类可以以不同的方式实现这些抽象方法，从而对剩余的逻辑有不同的实现。先构建一个顶级逻辑框架，而将逻辑的细节留给具体的子类去实现。 |
| 访问者     | 封装一些施加于某种数据结构元素之上的操作。一旦这些操作需要修改，接受这个操作的数据结构可以保持不变。访问者模式适用于数据结构相对未定的系统，它把数据结构和作用于结构上的操作之间的耦合解脱开，使得操作集合可以相对自由的演化。 |

当你看完上面这些介绍，可能你已经感受到了来自设计模式的压力，我也不例外，虽然我了解一些设计模式的知识，但是面对上面总结的各种模式，我也感到很无力，毕竟这是无数大牛精心设计且经过实践证明的''真理''。但是，越是这样的技术，越具有挑战性，你只要完全掌握上面内容的三分之一，你的编程水平已经上了一个台阶。了解并掌握设计模式的思想和原理，不仅有助于你写出优质健壮的代码，也将极大地提高系统的性能。同时你也将更容易的看懂他人优秀的代码。

Laravel 框架无疑是 PHP 中最优秀的框架之一，其优秀的原因在于他的先进的理念设计，优雅的代码结构，以及灵活的使用了大量的设计模式，使得框架非常稳健且易于扩展。所以，了解并掌握必要的设计模式的知识，是编程进阶的基础。

在本课程中，我将会根据相关资料参考，从三类设计模式挑选16个常用的设计模式来讲解，分为两个实验。

##  UML类图和时序图

如果你之前没有听说过或者接触过 UML ，那么可以在此处简单了解一下，更多详细的资料大家自行去查阅教程。

这里简单介绍一下UML类图和时序图的要点，让你可以看懂后续文档中给出的类图或时序图，可以更形象的帮助你理解设计模式。（UML内容较复杂，希望大家私下能去多了解一些相关知识）

首先，下面是一张典型的UML类图：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1478843259466.png/wm)

> - 车的类图结构为<>，表示车是一个抽象类；
>
> - 它有两个继承类：小汽车和自行车；它们之间的关系为`实现`关系，使用带实心箭头的虚线表示；
>
> - 小汽车为与SUV之间也是继承关系，它们之间的关系为`泛化`关系，使用带空心箭头的实线表示；
>
> - 小汽车与发动机之间是`组合`关系，使用带实心菱形的实线表示；
>
> - 学生与班级之间是`聚合`关系，使用带空心菱形的实线表示；
>
> - 学生与身份证之间为`关联`关系，使用一根实线表示；
>
> - 学生上学需要用到自行车，与自行车是一种`依赖`关系，使用带箭头的虚线表示；
>
>   --上述描述参考：[ Graphic Design Patterns](https://design-patterns.readthedocs.io/zh_CN/latest/index.html)

#### UML 类图与类的关系

部分内容参考：[UML类图与类的关系详解](http://www.uml.org.cn/oobject/201104212.asp)

向大家推荐一个在线UML类图制作工具：[processon](http://www.processon.com/)

类的关系有泛化(Generalization)、实现（Realization）、依赖(Dependency)和关联(Association)。其中关联又分为一般关联关系和聚合关系(Aggregation)，合成关系(Composition)

类图（Class Diagram）: 类图是面向对象系统建模中最常用和最重要的图，是定义其它图的基础。类图主要是用来显示系统中的类、接口以及它们之间的静态结构和关系的一种静态模型。

类图的3个基本组件：类名、属性、方法。 

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479198500927.png/wm)

#### 泛化(generalization)

表示`is-a`的关系，是对象之间耦合度最大的一种关系，子类继承父类的所有细节。直接使用语言中的继承表达。在类图中使用带三角空心箭头的实线表示，箭头从子类指向父类。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1519615832951.png/wm)

#### 实现（Realization）

在类图中就是接口和实现的关系。在类图中使用带三角实心箭头的虚线表示，箭头从实现类指向接口。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479199007927.png/wm)

#### 关联关系(association)

关联关系是用一条带箭头的直线表示的；它描述不同类的对象之间的结构关系；它是一种静态关系， 通常与运行状态无关，一般由常识等因素决定的；它一般用来定义对象之间静态的、天然的结构； 所以，关联关系是一种“强关联”的关系；

学生与学校是一种关联关系。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479199479813.png/wm)

#### 依赖(Dependency)

依赖关系是用一套带箭头的虚线表示的；如下图表示`A依赖于B`；他描述一个对象在运行期间会用到另一个对象的关系；

对象之间最弱的一种关联方式，是临时性的关联。代码中一般指由局部变量、函数参数、返回值建立的对于其他对象的调用关系。一个类调用被依赖类中的某些方法而得以完成这个类的一些职责。在类图使用带箭头的虚线表示，箭头从使用类指向被依赖的类。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479199678874.png/wm)

#### 聚合(Aggregation)

表示`has-a`的关系，是一种不稳定的包含关系。较强于一般关联,有整体与局部的关系,并且没有了整体,局部也可单独存在。如公司和员工的关系，公司包含员工，但如果公司倒闭，员工依然可以换公司。在类图使用空心的菱形表示，菱形从局部指向整体。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479199877080.png/wm)

#### 组合(Composition)

表示`contains-a`的关系，是一种强烈的包含关系。组合类负责被组合类的生命周期。是一种更强的聚合关系。部分不能脱离整体存在。如公司和部门的关系，没有了公司，部门也不能存在了；调查问卷中问题和选项的关系；订单和订单选项的关系。在类图使用实心的菱形表示，菱形从局部指向整体。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479199980657.png/wm)

#### 聚合和组合的区别

这两个比较难理解，重点说一下。聚合和组合的区别在于：聚合关系是“has-a”关系，组合关系是“contains-a”关系；聚合关系表示整体与部分的关系比较弱，而组合比较强；聚合关系中代表部分事物的对象与代表聚合事物的对象的生存期无关，一旦删除了聚合对象不一定就删除了代表部分事物的对象。组合中一旦删除了组合对象，同时也就删除了代表部分事物的对象。

此外，还有 UML 时序图，这部分就留给大家自行去了解学习，此处不做介绍

## 工厂模式

工厂模式具体可分为三类模式：简单工厂模式，工厂方法模式，抽象工厂模式；

### 1.**简单工厂模式**

> 又称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。在简单工厂模式中，可以根据`参数的不同`返回`不同类的实例`。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

`角色：`

Factory类：负责创建具体产品的实例

Product类：抽象产品类，定义产品子类的公共接口

ConcreteProduct 类：具体产品类，实现Product父类的接口功能，也可添加自定义的功能

`UML类图：`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479202997019.png/wm)

`示例代码：`:`Factory.class.php`

```php
<?php 
//简单工厂模式
class Cat
{
    function __construct()
    {
        echo "I am Cat class <br>";
    }
}
class Dog
{
    function __construct()
    {
        echo "I am Dog class <br>";
    }
}
class Factory
{
    public static function CreateAnimal($name){
        if ($name == 'cat') {
            return new Cat();
        } elseif ($name == 'dog') {
            return new Dog();
        }
    }
}

$cat = Factory::CreateAnimal('cat');
$dog = Factory::CreateAnimal('dog');
```

简单工厂模式最大的优点在于实现对象的创建和对象的使用分离，将对象的创建交给专门的工厂类负责，但是其最大的缺点在于工厂类不够灵活，增加新的具体产品需要修改工厂类的判断逻辑代码，而且产品较多时，工厂方法代码将会非常复杂。

------

### 2.**工厂方法模式**

此模式中，通过定义一个抽象的核心工厂类，并定义创建产品对象的`接口`，创建具体产品实例的工作延迟到其工厂子类去完成。这样做的好处是核心类只关注`工厂类的接口定义`，而具体的产品实例交给具体的`工厂子类`去创建。当系统需要新增一个产品，无需修改现有系统代码，只需要添加一个具体产品类和其对应的工厂子类，使系统的扩展性变得很好，符合面向对象编程的`开闭原则`;

`角色：`

Product：抽象产品类

ConcreteProduct：具体产品类

Factory：抽象工厂类

ConcreteFactory：具体工厂类

`UML类图：`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486374011050.png/wm)

`示例代码：`:`ConcreteFactory.class.php`

```php
<?php 
interface Animal{
    public function run();
    public function say();
}
class Cat implements Animal
{
    public function run(){
        echo "I ran slowly <br>";
    }
    public function say(){
        echo "I am Cat class <br>";
    }
}
class Dog implements Animal
{
    public function run(){
        echo "I'm running fast <br>";
    }
    public function say(){
        echo "I am Dog class <br>";
    }
}
abstract class Factory{
    abstract static function createAnimal();
}
class CatFactory extends Factory
{
    public static function createAnimal()
    {
        return new Cat();
    }
}
class DogFactory extends Factory
{
    public static function createAnimal()
    {
        return new Dog();
    }
}

$cat = CatFactory::createAnimal();
$cat->say();
$cat->run();

$dog = DogFactory::createAnimal();
$dog->say();
$dog->run();
```

工厂方法模式是简单工厂模式的进一步抽象和推广。由于使用了面向对象的多态性，工厂方法模式保持了简单工厂模式的优点，而且克服了它的缺点。在工厂方法模式中，核心的工厂类不再负责所有产品的创建，而是将具体创建工作交给子类去做。这个核心类仅仅负责给出具体工厂必须实现的接口，而不负责产品类被实例化这种细节，这使得工厂方法模式可以允许系统在不修改工厂角色的情况下引进新产品。

------

### 3.**抽象工厂模式**

提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂模式又称为Kit模式，属于对象创建型模式。

此模式是对工厂方法模式的进一步扩展。在工厂方法模式中，一个具体的工厂负责生产一类具体的产品，即一对一的关系，但是，如果需要一个具体的工厂生产多种产品对象，那么就需要用到抽象工厂模式了。

为了便于理解此模式，这里介绍两个概念：

- **产品等级结构**：产品等级结构即产品的继承结构，如一个抽象类是电视机，其子类有海尔电视机、海信电视机、TCL电视机，则抽象电视机与具体品牌的电视机之间构成了一个产品等级结构，抽象电视机是父类，而具体品牌的电视机是其子类。

- **产品族 ：**在抽象工厂模式中，产品族是指由同一个工厂生产的，位于不同产品等级结构中的一组产品，如海尔电器工厂生产的海尔电视机、海尔电冰箱，海尔电视机位于电视机产品等级结构中，海尔电冰箱位于电冰箱产品等级结构中。

  `角色：`

  抽象工厂（AbstractFactory）：担任这个角色的是抽象工厂模式的核心，是与应用系统的商业逻辑无关的。

  具体工厂（Factory）：这个角色直接在客户端的调用下创建产品的实例，这个角色含有选择合适的产品对象的逻辑，而这个逻辑是与应用系统商业逻辑紧密相关的。

  抽象产品（AbstractProduct）：担任这个角色的类是抽象工厂模式所创建的对象的父类，或它们共同拥有的接口

  具体产品（Product）：抽象工厂模式所创建的任何产品对象都是一个具体的产品类的实例。

  `UML类图：`

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479204958020.png/wm)

  `示例代码：`:`AbstructFactory.class.php`

  ```php
  <?php 
  
  interface TV{
    public function open();
    public function watch();
  }
  
  class HaierTv implements TV
  {
    public function open()
    {
        echo "Open Haier TV <br>";
    }
  
    public function watch()
    {
        echo "I'm watching Haier TV <br>";
    }
  }
  
  class LenovoTv implements TV
  {
    public function open()
    {
        echo "Open Lenovo TV <br>";
    }
  
    public function watch()
    {
        echo "I'm watching Lenovo TV <br>";
    }
  }
  
  interface PC{
    public function work();
    public function play();
  }
  
  class LenovoPc implements PC
  {
    public function work()
    {
        echo "I'm working on a Lenovo computer <br>";
    }
    public function play()
    {
        echo "Lenovo computers can be used to play games <br>";
    }
  }
  
  class HaierPc implements PC
  {
    public function work()
    {
        echo "I'm working on a Haier computer <br>";
    }
    public function play()
    {
        echo "Haier computers can be used to play games <br>";
    }
  }
  
  abstract class Factory{
    abstract public static function createPc();
    abstract public static function createTv();
  }
  
  class HaierFactory extends Factory
  {
      public static function createTv()
      {
          return new HaierTv();
      }
  
      public static function createPc()
      {
          return new HaierPc();
      }
  
  }
  
  class LenovoFactory extends Factory
  {
      public static function createTv()
      {
          return new LenovoTv();
      }
  
      public static function createPc()
      {
          return new LenovoPc();
      }
  
  }
  
  $haierFactory = new HaierFactory();
  $haierTv = $haierFactory->createTv();
  $haierTv->open();
  $haierTv->watch();
  
  $haierPc = $haierFactory->createPc();
  $haierPc->work();
  $haierPc->play();
  
  $lenovoFactory = new LenovoFactory();
  $lenovoPc = $lenovoFactory->createPc();
  $lenovoPc->work();
  $lenovoPc->play();
  
  $lenovoTv = $lenovoFactory->createTv();
  $lenovoTv->open();
  $lenovoTv->watch();
  ```

## 建造者模式

又名：**生成器模式**，是一种对象构建模式。它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。

建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。例如，一辆汽车由轮子，发动机以及其他零件组成，对于普通人而言，我们使用的只是一辆完整的车，这时，我们需要加入一个构造者，让他帮我们把这些组件按序组装成为一辆完整的车。

`角色：`

Builder：抽象构造者类，为创建一个Product对象的各个部件指定抽象接口。

ConcreteBuilder：具体构造者类，实现Builder的接口以构造和装配该产品的各个部件。定义并明确它所创建的表示。提供一个检索产品的接口

Director：指挥者，构造一个使用Builder接口的对象。

Product：表示被构造的复杂对象。ConcreateBuilder创建该产品的内部表示并定义它的装配过程。

包含定义组成部件的类，包括将这些部件装配成最终产品的接口。

`UML类图：`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486373749271.png/wm)

```php
示例代码：`:`Builder.class.php
<?php 
/**
* chouxiang builer
*/
abstract class Builder
{
    protected $car;
    abstract public function buildPartA();
    abstract public function buildPartB();
    abstract public function buildPartC();
    abstract public function getResult();
}

class CarBuilder extends Builder
{
    function __construct()
    {
        $this->car = new Car();
    }
    public function buildPartA(){
        $this->car->setPartA('发动机');
    }

    public function buildPartB(){
        $this->car->setPartB('轮子');
    }

    public function buildPartC(){
        $this->car->setPartC('其他零件');
    }

    public function getResult(){
        return $this->car;
    }
}

class Car
{
    protected $partA;
    protected $partB;
    protected $partC;

    public function setPartA($str){
        $this->partA = $str;
    }

    public function setPartB($str){
        $this->partB = $str;
    }

    public function setPartC($str){
        $this->partC = $str;
    }

    public function show()
    {
        echo "这辆车由：".$this->partA.','.$this->partB.',和'.$this->partC.'组成';
    }
}

class Director
{
    public $myBuilder;

    public function startBuild()
    {
        $this->myBuilder->buildPartA();
        $this->myBuilder->buildPartB();
        $this->myBuilder->buildPartC();
        return $this->myBuilder->getResult();
    }

    public function setBuilder(Builder $builder)
    {
        $this->myBuilder = $builder;
    }
}

$carBuilder = new CarBuilder();
$director = new Director();
$director->setBuilder($carBuilder);
$newCar = $director->startBuild();
$newCar->show();
```

## 单例模式

> **单例模式**，也叫**单子模式**，是一种常用的[软件设计模式](https://zh.wikipedia.org/wiki/软件设计模式)。在应用这个模式时，单例对象的[类](https://zh.wikipedia.org/wiki/类)必须保证只有一个实例存在。许多时候整个系统只需要拥有一个的全局[对象](https://zh.wikipedia.org/wiki/对象)，这样有利于我们协调系统整体的行为。
>
> 实现单例模式的思路是：一个类能返回对象一个引用(永远是同一个)和一个获得该实例的方法（必须是静态方法，通常使用getInstance这个名称）；当我们调用这个方法时，如果类持有的引用不为空就返回这个引用，如果类保持的引用为空就创建该类的实例并将实例的引用赋予该类保持的引用；同时我们还将该类的[构造函数](https://zh.wikipedia.org/wiki/构造函数)定义为私有方法，这样其他处的代码就无法通过调用该类的构造函数来实例化该类的对象，只有通过该类提供的静态方法来得到该类的唯一实例。
>
> ---维基百科

单例模式的要点有：某个类只能有一个实例；它必须自行创建本身的实例；它必须自行向整个系统提供这个实例。单例模式是一种对象创建型模式。

`角色：`

Singleton：单例类

`UML 类图：`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486368498821.png/wm)

```php
示例代码``Singleton.class.php
<?php 

class Singleton
{
    private static $instance;
    //私有构造方法，禁止使用new创建对象
    private function __construct(){}

    public static function getInstance(){
        if (!isset(self::$instance)) {
            self::$instance = new self;
        }
        return self::$instance;
    }
    //将克隆方法设为私有，禁止克隆对象
    private function __clone(){}

    public function say()
    {
        echo "这是用单例模式创建对象实例 <br>";
    }
    public function operation()
    {
        echo "这里可以添加其他方法和操作 <br>";
    }
}

// $shiyanlou = new Singleton();
$shiyanlou = Singleton::getInstance();
$shiyanlou->say();
$shiyanlou->operation();

$newShiyanlou = Singleton::getInstance();
var_dump($shiyanlou === $newShiyanlou);
```

------

上述的几个模式均属于`创建型模式`，接下来将要介绍的模式属于结构型模式，在本实验后面的文档将介绍三个，剩下来的留在下一个实验继续介绍。

## 适配器模式

> 在[设计模式](https://zh.wikipedia.org/wiki/设计模式_(计算机)中，**适配器模式**（英语：adapter pattern）有时候也称包装样式或者包装(wrapper)。将一个[类](https://zh.wikipedia.org/wiki/类_(计算机科学))的接口转接成用户所期待的。一个适配使得因接口不兼容而不能在一起工作的类工作在一起，做法是将类自己的接口包裹在一个已存在的类中。
>
> ---维基百科

顾名思义，此模式是源于类似于电源适配器的设计和编码技巧。比如现在有一些类，提供一些可用的接口，但是可能客户端因为不兼容的原因，不能直接调用这些现有的接口，这时就需要一个适配器来作为中转站，适配器类可以向用户提供可用的接口，其内部将收到的请求转换为对适配者对应接口的真实请求，从而实现对不兼容的类的复用。

优点：将目标类和适配者类解耦，通过引入一个适配器类来重用现有的适配者类，而无须修改原有代码。增加了类的透明性和复用性，将具体的实现封装在适配者类中，对于客户端类来说是透明的，而且提高了适配者的复用性。

`角色：`

Target：目标抽象类

Adapter：适配器类

Adaptee：适配者类

Client：客户类

`UML 类图:`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486370324222.png/wm)

```php
示例代码:`:`Adapter.class.php
<?php 

class Adaptee
{
    public function realRequest()
    {
        echo "这是被适配者真正的调用方法";
    }
}

interface Target{
    public function request();
}

class Adapter implements Target
{
    protected $adaptee;
    function __construct(Adaptee $adaptee)
    {
        $this->adaptee = $adaptee;
    }

    public function request()
    {
        echo "适配器转换：";
        $this->adaptee->realRequest();
    }
}

$adaptee = new Adaptee();
$target = new Adapter($adaptee);
$target->request();
```

## 桥接模式

桥接模式是[软件设计模式](https://zh.wikipedia.org/wiki/軟件設計模式)中最复杂的模式之一，它把事物对象和其具体行为、具体特征分离开来，使它们可以各自独立的变化。事物对象仅是一个抽象的概念。如“圆形”、“三角形”归于抽象的“形状”之下，而“画圆”、“画三角”归于实现行为的“画图”类之下，然后由“形状”调用“画图”。

理解桥接模式，重点需要理解如何将抽象化(Abstraction)与实现化(Implementation)脱耦，使得二者可以独立地变化。桥接模式提高了系统的可扩充性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统。

`角色：`

Abstraction：定义抽象的接口，该接口包含实现具体行为、具体特征的Implementor接口

Refined Abstraction：抽象接口Abstraction的子类，依旧是一个抽象的事物名

Implementor：定义具体行为、具体特征的应用接口

ConcreteImplementor：实现Implementor接口

`UML类图`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486371373063.png/wm)

```php
示例代码` `DrawingAPI.class.php
<?php 
interface DrawingAPI{
    public function drawCircle($x,$y,$radius);
}

/**
* drawAPI1
*/
class DrawingAPI1 implements DrawingAPI
{
    public function drawCircle($x,$y,$radius)
    {
        echo "API1.circle at (".$x.','.$y.') radius '.$radius.'<br>';
    }
}

/**
* drawAPI2
*/
class DrawingAPI2 implements DrawingAPI
{
    public function drawCircle($x,$y,$radius)
    {
        echo "API2.circle at (".$x.','.$y.') radius '.$radius.'<br>';
    }
}

/**
*shape接口
*/
interface Shape{
    public function draw();
    public function resize($radius);
}

class CircleShape implements Shape
{
    private $x;
    private $y;
    private $radius;
    private $drawingAPI;
    function __construct($x,$y,$radius,DrawingAPI $drawingAPI)
    {
        $this->x = $x;
        $this->y = $y;
        $this->radius = $radius;
        $this->drawingAPI = $drawingAPI;
    }

    public function draw()
    {
        $this->drawingAPI->drawCircle($this->x,$this->y,$this->radius);
    }

    public function resize($radius)
    {
        $this->radius = $radius;
    }
}

$shape1 = new CircleShape(1,2,4,new DrawingAPI1());
$shape2 = new CircleShape(1,2,4,new DrawingAPI2());
$shape1->draw();
$shape2->draw();
$shape1->resize(10);
$shape1->draw();
```

------

## 装饰器模式

**修饰模式**，是[面向对象编程](https://zh.wikipedia.org/wiki/面向对象编程)领域中，一种动态地往一个类中添加新的行为的[设计模式](https://zh.wikipedia.org/wiki/软件设计模式)。就功能而言，修饰模式相比生成[子类](https://zh.wikipedia.org/wiki/子类)更为灵活，这样可以给某个对象而不是整个类添加一些功能。

一般来说，给一个对象或者类增加行为的方式可以有两种：

- 继承机制，使用继承机制是给现有类添加功能的一种有效途径，通过继承一个现有类可以使得子类在拥有自身方法的同时还拥有父类的方法。但是这种方法是静态的，用户不能控制增加行为的方式和时机。

- 关联机制，即将一个类的对象嵌入另一个对象中，由另一个对象来决定是否调用嵌入对象的行为以便扩展自己的行为，我们称这个嵌入的对象为装饰器(Decorator)

  通过使用修饰模式，可以在运行时扩充一个类的功能。原理是：增加一个修饰类包裹原来的类，包裹的方式一般是通过在将原来的对象作为修饰类的构造函数的参数。装饰类实现新的功能，但是，在不需要用到新功能的地方，它可以直接调用原来的类中的方法。修饰类必须和原来的类有相同的接口。

  修饰模式是类继承的另外一种选择。类继承在编译时候增加行为，而装饰模式是在运行时增加行为。

  `角色`

  Component: 抽象构件

  ConcreteComponent: 具体构件

  Decorator: 抽象装饰类

  ConcreteDecorator: 具体装饰类

  `UML 类图`

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2293timestamp1479221663191.png/wm)

  `示例代码`：`Component.class.php`

  ```php
  <?php 
  abstract class Component {
    abstract public function operation();
  }
  
  class MyComponent extends Component
  {
    public function operation()
    {
        echo "这是正常的组件方法 <br>";
    }
  }
  
  abstract class Decorator extends Component {
    protected $component;
    function __construct(Component $component)
    {
        $this->component = $component;
    }
  
    public function operation()
    {
        $this->component->operation();
    }
  }
  
  class MyDecorator extends Decorator
  {
  
    function __construct(Component $component)
    {
        parent::__construct($component);
    }
  
    public function addMethod()
    {
        echo "这是装饰器添加的方法 <br>";
    }
  
    public function operation()
    {
        $this->addMethod();
        parent::operation();
    }
  }
  
  $component = new MyComponent();
  $da = new MyDecorator($component);
  $da->operation();
  ```

## 外观模式

外观模式(Facade Pattern)：外部与一个子系统的通信必须通过一个统一的外观对象进行，为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。外观模式又称为门面模式，它是一种对象结构型模式。

举一个简单的例子，相信大家都使用过 C++ 语言，他是一门编译型语言，写完代码之后，我们需要经过编译之后才能运行，在IDE中，会有一个 Build 的按钮，点击它即可完成编译过程，但是这一个简单的动作背后，却是一系列复杂操作的协调配合，至少包括词法分析，语法分析，生成中间代码，生成汇编代码以及链接等操作，作为普通开发人员，我们不必在意这些过程是如何完成的，只需要点击Build按钮，IDE就会自动帮我们完成背后的工作。那么这个Build按钮就是IDE为我们提供的高级接口，通过他来完成各种子系统的协调工作。

`角色：`

Facade：外观角色，提供高级接口

SubSystem：子系统角色，负责各自的功能实现

`UML 类图`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486374473372.png/wm)

```php
示例代码`：`Facade.class.php
<?php 

class SystemA
{
    public function operationA()
    {
        echo "operationA <br>";
    }
}

class SystemB
{
    public function operationB()
    {
        echo "operationB <br>";
    }
}

class SystemC
{
    public function operationC()
    {
        echo "operationC <br>";
    }
}

class Facade
{
    protected $systemA;
    protected $systemB;
    protected $systemC;

    function __construct()
    {
        $this->systemA = new SystemA();
        $this->systemB = new SystemB();
        $this->systemC = new SystemC();
    }

    public function myOperation()
    {
        $this->systemA->operationA();
        $this->systemB->operationB();
        $this->systemC->operationC();
    }
}

$facade = new Facade();
$facade->myOperation();
```

使用外观模式最大的优点就是子系统与客户端之间是松耦合的关系，客户端不必知道具体有哪些子系统，也无需知道他们是如何工作的，通过引入一个外观类，提供一个客户端间接访问子系统的高级接口。子系统和外观类可以独立运作，修改某一个子系统的内容，不会影响到其他子系统，也不会影响到外观对象。不过它的缺点就是它不够灵活，当需要增加一个子系统的时候，需要修改外观类。

## 享元模式

- [**享元模式**](https://design-patterns.readthedocs.io/zh_CN/latest/structural_patterns/flyweight.html)

  **享元模式**（英语：Flyweight Pattern）是一种软件[设计模式](https://zh.wikipedia.org/wiki/设计模式_(计算机))。它使用共享物件，用来尽可能减少内存使用量以及分享资讯给尽可能多的相似物件；它适合用于当大量物件只是重复因而导致无法令人接受的使用大量内存。通常物件中的部分状态是可以分享。常见做法是把它们放在外部数据结构，当需要使用时再将它们传递给享元。由于享元模式要求能够共享的对象必须是细粒度对象，因此它又称为轻量级模式，它是一种对象结构型模式。

  要理解享元模式，先要理解两个重要的概念：内部状态和外部状态。

  内部状态存储于flyweight中，它包含了独立于flyweight场景的信息，这些信息使得flyweight可以被共享。而外部状态取决于flyweight场景，并根据场景而变化，因此不可共享。用户对象负责在必要的时候将外部状态传递给flyweight。

  `角色`

  Flyweight： 抽象享元类

  ConcreteFlyweight： 具体享元类

  UnsharedConcreteFlyweight： 非共享具体享元类

  FlyweightFactory： 享元工厂类

  `UML类图`

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486374834526.png/wm)

  `示例代码`：`FlyWeight.class.php`

  ```php
  <?php
  interface Flyweight{
      public function operation();
  }
  
  class MyFlyweight implements Flyweight
  {
      protected $intrinsicState;
      function __construct($str)
      {
          $this->intrinsicState = $str;
      }
  
      public function operation()
      {
          echo 'MyFlyweight['.$this->intrinsicState.'] do operation. <br>';
      }
  }
  
  class FlyweightFactory
  {
      protected static $flyweightPool;
      function __construct()
      {
          if (!isset(self::$flyweightPool)) {
              self::$flyweightPool = [];
          }
      }
      public function getFlyweight($str)
      {
  
          if (!array_key_exists($str,self::$flyweightPool)) {
              $fw = new MyFlyweight($str);
              self::$flyweightPool[$str] = $fw;
              return $fw;
          } else {
              echo "aready in the pool,use the exist one: <br>";
              return self::$flyweightPool[$str];
          }
  
      }
  }
  
  $factory = new FlyweightFactory();
  $fw = $factory->getFlyweight('one');
  $fw->operation();
  
  $fw1 = $factory->getFlyweight('two');
  $fw1->operation();
  
  $fw2 = $factory->getFlyweight('one');
  $fw2->operation();
  ```

  享元模式的核心在于享元工厂类，享元工厂类的作用在于提供一个用于存储享元对象的享元池，用户需要对象时，首先从享元池中获取，如果享元池中不存在，则创建一个新的享元对象返回给用户，并在享元池中保存该新增对象。

## 代理模式

所谓的代理者是指一个类别可以作为其它东西的接口。代理者可以作任何东西的接口：网络连接、内存中的大对象、文件或其它昂贵或无法复制的资源。

代理对象可以在客户端和目标对象之间起到 中介的作用，并且可以通过代理对象去掉客户不能看到 的内容和服务或者添加客户需要的额外服务。

可能大家听得最多且最常用的就是VPN网络代理，或者代理服务器等。

```
角色
```

Subject: 抽象主题角色

Proxy: 代理主题角色

RealSubject: 真实主题角色

```
UML类图
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486375056301.png/wm)

```
示例代码`：`Proxy.class.php
<?php 
interface Subject{
    public function request();
}

class RealSubject implements Subject
{
    public function request()
    {
        echo "RealSubject::request <br>";
    }
}

class Proxy implements Subject
{
    protected $realSubject;
    function __construct()
    {
        $this->realSubject = new RealSubject();
    }

    public function beforeRequest()
    {
        echo "Proxy::beforeRequest <br>";
    }

    public function request()
    {
        $this->beforeRequest();
        $this->realSubject->request();
        $this->afterRequest();
    }

    public function afterRequest()
    {
        echo "Proxy::afterRequest <br>";
    }
}

$proxy = new Proxy();
$proxy->request();
```

下面将会介绍五种行为型模式。

## 命令模式

在软件设计中，我们有时需要向某些对象发送请求(命令)，但是对于请求(命令)发送者来说，并不知道请求(命令)的接收者是谁，它只需要发送命令即可；对于请求(命令)的接受者来说，他也并不知道给他发送请求(命令)的是谁，它只需要在有请求(命令)时执行自己的 action 即可。具体的请求(命令)发送者和接受者，我们就可以根据自己的需求自由组合。使得请求发送者与请求接收者消除彼此之间的耦合，让对象之间的调用关系更加灵活。

主要特点就是将一个请求封装为一个对象，从而使我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。命令模式是一种对象行为型模式，其别名为动作(Action)模式或事务(Transaction)模式。

```
角色
```

Command: 抽象命令类

ConcreteCommand: 具体命令类

Invoker: 调用者

Receiver: 接收者

Client:客户类

```
UML类图
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486375353023.png/wm)

```
示例代码`：`Command.class.php
<?php 
class Receiver
{
    public function Action()
    {
        echo "Receiver->Action";
    }
}

abstract class Command{
    protected $receiver;
    function __construct(Receiver $receiver)
    {
        $this->receiver = $receiver;
    }
    abstract public function Execute();
}

class MyCommand extends Command
{
    function __construct(Receiver $receiver)
    {
        parent::__construct($receiver);
    }

    public function Execute()
    {
        $this->receiver->Action();
    }
}

class Invoker
{
    protected $command;
    function __construct(Command $command)
    {
        $this->command = $command;
    }

    public function Invoke()
    {
        $this->command->Execute();
    }
}

$receiver = new Receiver();
$command = new MyCommand($receiver);
$invoker = new Invoker($command);
$invoker->Invoke();
```

## 中介者模式

《设计模式:可复用面向对象软件的基础》一书中对中介者模式定义：用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。

举个简单的例子，就比如大家平时喜欢用微信聊天，你发送的聊天内容需要通过微信服务器进行中间处理，然后下发给你的好友，微信服务器就是一个中介者。

```
角色
```

Mediator: 抽象中介者

ConcreteMediator: 具体中介者

Colleague: 抽象同事类

ConcreteColleague: 具体同事类

```
UML类图
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486375717275.png/wm)

```
示例代码`：`Mediator.class.php
<?php 

abstract class Colleague{
    protected $mediator;
    abstract public function sendMsg($who,$msg);
    abstract public function receiveMsg($msg);
    public function setMediator(Mediator $mediator){
        $this->mediator = $mediator;
    }
}

class ColleagueA extends Colleague
{
    public function sendMsg($toWho,$msg)
    {
        echo "Send Msg From ColleagueA To: ".$toWho . '<br>';
        $this->mediator->opreation($toWho,$msg);
    }

    public function receiveMsg($msg)
    {
        echo "ColleagueA Receive Msg: ".$msg . '<br>';
    }
}

class ColleagueB extends Colleague
{
    public function sendMsg($toWho,$msg)
    {
        echo "Send Msg From ColleagueB To: ".$toWho . '<br>';
        $this->mediator->opreation($toWho,$msg);
    }

    public function receiveMsg($msg)
    {
        echo "ColleagueB Receive Msg: ".$msg . '<br>';
    }
}

abstract class Mediator{
    abstract public function opreation($id,$message);
    abstract public function register($id,Colleague $colleague);
}

class MyMediator extends Mediator
{
    protected static $colleagues;
    function __construct()
    {
        if (!isset(self::$colleagues)) {
            self::$colleagues = [];
        }
    }

    public function opreation($id,$message)
    {
        if (!array_key_exists($id,self::$colleagues)) {
            echo "colleague not found";
            return;
        }
        $colleague = self::$colleagues[$id];
        $colleague->receiveMsg($message);
    }

    public function register($id,Colleague $colleague)
    {
        if (!in_array($colleague, self::$colleagues)) {
            self::$colleagues[$id] = $colleague;
        }
        $colleague->setMediator($this);
    }
}

$colleagueA = new ColleagueA();
$colleagueB = new ColleagueB();
$mediator = new MyMediator();
$mediator->register(1,$colleagueA);
$mediator->register(2,$colleagueB);
$colleagueA->sendMsg(2,'hello admin');
$colleagueB->sendMsg(1,'shiyanlou');
```

中介者模式的两个主要作用：中转作用（结构性）：通过中介者提供的中转作用，各个同事对象就不再需要显式引用其他同事，当需要和其他同事进行通信时，通过中介者即可。该中转作用属于中介者在结构上的支持。

协调作用（行为性）：中介者可以更进一步的对同事之间的关系进行封装，同事可以一致地和中介者进行交互，而不需要指明中介者需要具体怎么做，中介者根据封装在自身内部的协调逻辑，对同事的请求进行进一步处理，将同事成员之间的关系行为进行分离和封装。该协调作用属于中介者在行为上的支持。

## 观察者模式

在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实时事件处理系统。观察者模式又叫做发布-订阅（Publish/Subscribe）模式、模型-视图（Model/View）模式、源-监听器（Source/Listener）模式或从属者（Dependents）模式。

`角色`

Subject: 抽象目标类，一般至少提供三个接口：

- 添附(Attach)：新增观察者到串炼内，以追踪目标对象的变化。

- 解附(Detach)：将已经存在的观察者从串炼中移除。

- 通知(Notify)：利用观察者所提供的更新函式来通知此目标已经产生变化。

  ConcreteSubject: 具体目标，提供了观察者欲追踪的状态，也可设置目标状态

  Observer: 抽象观察者，定义观察者的更新操作接口

  ConcreteObserver: 具体观察者，实现抽象观察者的接口，做出自己的更新操作

  `UML类图`

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486376064027.png/wm)

  `示例代码`：`Observer.class.php`

  ```php
  <?php 
  
  abstract class Obeserver{
    abstract function update(Subject $sub);
  }
  
  abstract class Subject{
    protected static $obeservers;
    function __construct()
    {
        if (!isset(self::$obeservers)) {
            self::$obeservers = [];
        }
    }
    public function attach(Obeserver $obeserver){
        if (!in_array($obeserver, self::$obeservers)) {
            self::$obeservers[] = $obeserver;
        }
    }
    public function deattach(Obeserver $obeserver){
        if (in_array($obeserver, self::$obeservers)) {
            $key = array_search($obeserver,self::$obeservers);
            unset(self::$obeservers[$key]);
        }
    }
    abstract public function setState($state);
    abstract public function getState();
    public function notify()
    {
        foreach (self::$obeservers as $key => $value) {
            $value->update($this);
        }
    }
  }
  
  class MySubject extends Subject
  {
    protected $state;
    public function setState($state)
    {
        $this->state = $state;
    }
  
    public function getState()
    {
        return $this->state;
    }
  }
  
  class MyObeserver extends Obeserver
  {
    protected $obeserverName;
    function __construct($name)
    {
        $this->obeserverName = $name;
    }
    public function update(Subject $sub)
    {
        $state = $sub->getState();
        echo "Update Obeserver[".$this->obeserverName.'] State: '.$state . '<br>';
    }
  }
  
  $subject = new MySubject();
  $one = new MyObeserver('one');
  $two = new MyObeserver('two');
  
  $subject->attach($one);
  $subject->attach($two);
  $subject->setState(1);
  $subject->notify();
  echo "--------------------- <br>";
  $subject->setState(2);
  $subject->deattach($two);
  $subject->notify();
  ```

  主要作用：

- 当抽象个体有两个互相依赖的层面时。封装这些层面在单独的对象内将可允许程序员单独地去变更与重复使用这些对象，而不会产生两者之间交互的问题。

- 当其中一个对象的变更会影响其他对象，却又不知道多少对象必须被同时变更时。

- 当对象应该有能力通知其他对象，又不应该知道其他对象的实做细节时。

## 状态模式

状态模式：允许一个对象在其内部状态改变时改变它的行为。对象看起来似乎修改了它的类。其别名为状态对象(Objects for States)，状态模式是一种对象行为型模式。

有时，一个对象的行为受其一个或多个具体的属性变化而变化，这样的属性也叫作状态，这样的的对象也叫作有状态的对象。

`角色`

Context: 环境类，维护一个ConcreteState子类的实例，这个实例定义当前状态；

State: 抽象状态类，定义一个接口以封装与Context的一个特定状态相关的行为；

ConcreteState: 具体状态类，每一个子类实现一个与Context的一个状态相关的行为。

`UML类图`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486376254211.png/wm)

```php
示例代码`：`State.class.php
<?php 

class Context{
    protected $state;
    function __construct()
    {
        $this->state = StateA::getInstance();
    }
    public function changeState(State $state)
    {
        $this->state = $state;
    }

    public function request()
    {
        $this->state->handle($this);
    }
}

abstract class State{
    abstract function handle(Context $context);
}

class StateA extends State
{
    private static $instance;
    private function __construct(){}
    private function __clone(){}

    public static function getInstance()
    {
        if (!isset(self::$instance)) {
            self::$instance = new self;
        }
        return self::$instance;
    }

    public function handle(Context $context)
    {
        echo "doing something in State A.\n done,change state to B <br>";
        $context->changeState(StateB::getInstance());
    }
}

class StateB extends State
{
    private static $instance;
    private function __construct(){}
    private function __clone(){}

    public static function getInstance()
    {
        if (!isset(self::$instance)) {
            self::$instance = new self;
        }
        return self::$instance;
    }

    public function handle(Context $context)
    {
        echo "doing something in State B.\n done,change state to A <br>";
        $context->changeState(StateA::getInstance());
    }
}

$context = new Context();
$context->request();
$context->request();
$context->request();
$context->request();
```

## 策略模式

策略模式(Strategy Pattern)：定义一系列算法，将每一个算法封装起来，并让它们可以相互替换。策略模式让算法独立于使用它的客户而变化，也称为政策模式(Policy)。

常见示例：常见的排序算法有快速排序，冒泡排序，归并排序，选择排序等，如果我们需要在一个算法类中提供这些算法，一个常见的解决方法就是在类中定义多个方法，每个方法定义一种具体的排序算法，然后使用 if...else...去判断到底是哪种算法，或者直接调用某个具体方法。这种方法是将算法的实现硬编码到类中，这样做最大的弊端就是算法类类非常臃肿，而且当需要增加或者更换一种新的排序方法时候，需要修改算法类的代码，同时也需要修改客户端调用处的代码。策略模式就是为了解决这列问题而设计的。

`角色`

Context: 环境类，使用一个ConcreteStrategy对象来配置；维护一个对Stategy对象的引用，同时，可以定义一个接口来让Stategy访问它的数据。

Strategy: 抽象策略类，定义所有支持的算法的公共接口。Context使用这个接口来调用某ConcreteStrategy定义的算法；

ConcreteStrategy: 具体策略类，实现 Strategy 接口的具体算法；

`UML类图`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid108299labid2297timestamp1486376535542.png/wm)

```php
示例代码`：`Strategy.class.php
<?php 
abstract class Strategy{
    abstract function use();
}

class StrategyA extends Strategy
{
    public function use()
    {
        echo "这是使用策略A的方法 <br>";
    }
}

class StrategyB extends Strategy
{
    public function use()
    {
        echo "这是使用策略B的方法 <br>";
    }
}

class Context
{
    protected $startegy;
    public function setStrategy(Strategy $startegy)
    {
        $this->startegy = $startegy;
    }

    public function use()
    {
        $this->startegy->use();
    }
}

$context = new Context();
$startegyA = new StrategyA();
$startegyB = new StrategyB();
$context->setStrategy($startegyA);
$context->use();

$context->setStrategy($startegyB);
$context->use();
```