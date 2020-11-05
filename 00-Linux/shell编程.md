## 特殊字符

### 一、注释`#`

行首以 `#` 开头(除#!之外)的是注释。`#!`是用于指定当前脚本的解释器，我们这里为bash，且应该指明完整路径，所以为`/bin/bash`

当然，在echo中转义的 # 是不能作为注释的：

```sh
$ vim test.sh
```

输入如下代码，并保存。（中文为注释，不需要输入）

```sh
#!/bin/bash

echo "The # here does not begin a comment."
echo 'The # here does not begin a comment.'
echo The \# here does not begin a comment.
echo The # 这里开始一个注释
echo $(( 2#101011 ))     # 数制转换（使用二进制表示），不是一个注释，双括号表示对于数字的处理

# 行首开始是注释
```

执行脚本，查看输出

```sh
$ bash test.sh
```

> 上面的脚本说明了如何使用`echo`打印出一段字符串和变量内容，这里采用了几种不同的方式，希望你可以理解这几种不同方式的异同

### 二、分号`;`

#### 1.命令分隔符

使用分号（;）可以在同一行上写两个或两个以上的命令。

```sh
$ vim test2.sh
```

输入如下代码，并保存。

```sh
 #!/bin/bash
 echo hello; echo there
 filename=ttt.sh
 if [ -e "$filename" ]; then    # 注意: "if"和"then"需要分隔，-e用于判断文件是否存在
     echo "File $filename exists."; cp $filename $filename.bak
 else
     echo "File $filename not found."; touch $filename
 fi; echo "File test complete."
```

执行脚本

```sh
$ bash test2.sh
```

查看结果

```sh
$ ls
```

解释说明

上面脚本使用了一个if分支判断一个文件是否存在，如果文件存在打印相关信息并将该文件备份；如果不存在打印相关信息并创建一个新的文件。最后将输出"测试完成"。

#### 2.终止case选项（双分号）

使用双分号（;;）可以终止case选项。

```sh
$ vim test3.sh
```

输入如下代码，并保存。

```sh
#!/bin/bash

varname=b

case "$varname" in
    [a-z]) echo "abc";;
    [0-9]) echo "123";;
esac
```

执行脚本，查看输出

```sh
$ bash test3.sh
abc
```

> 上面脚本使用case语句，首先创建了一个变量初始化为b,然后使用case语句判断该变量的范围，并打印相关信息。如果你有其它编程语言的经验，这将很容易理解。

### 三、点号 `.`

#### 等价于 source 命令

bash 中的 source 命令用于在当前 bash 环境下读取并执行 FileName.sh 中的命令。

```sh
$ source test.sh
Hello World
$ . test.sh
Hello World
```

### 四、引号

#### 1.双引号（")

"STRING" 将会阻止（解释）STRING中大部分特殊的字符。

#### 2.单引号（'）

'STRING' 将会阻止STRING中所有特殊字符的解释，这是一种比使用"更强烈的形式。

#### 3. 区别

这里举一个例子，能够更加生动的说明

同样是`$HOME`，单引号会直接认为是**字符**，而双引号认为是一个**变量**

### 五、斜线和反斜线

#### 1.斜线（/）

**文件名路径分隔符**。分隔文件名不同的部分（如`/home/bozo/projects/Makefile`）。也可以用来作为除法算术操作符。注意在linux中表示路径的时候，许多个`/`跟一个`/`是一样的。`/home/shiyanlou`等同于`////home///shiyanlou`

#### 2.反斜线（\）

一种对单字符的引用机制。\X 将会“转义”字符X。这等价于"X"，也等价于'X'。\ 通常用来转义双引号（"）和单引号（'），这样双引号和单引号就不会被解释成特殊含义了。

+ 符号 说明
+ \n 表示新的一行
+ \r 表示回车
+ \t 表示水平制表符
+ \v 表示垂直制表符
+ \b 表示后退符
+ \a 表示"alert"(蜂鸣或者闪烁)
+ \0xx 转换为八进制的ASCII码, 等价于0xx
+ " 表示引号字面的意思

转义符也提供续行功能，也就是编写多行命令的功能。

每一个单独行都包含一个不同的命令，但是每行结尾的转义符都会转义换行符，这样下一行会与上一行一起形成一个命令序列。

### 六、反引号`

#### 命令替换

反引号中的命令会`优先执行`，如：

```sh
$ cp `mkdir back` test.sh back
$ ls
```

先创建了 back 目录，然后复制 test.sh 到 back 目录

### 七、冒号`:`

#### 1.空命令

等价于“NOP”（no op，一个什么也不干的命令）。也可以被认为与shell的内建命令true作用相同。

“:”命令是一个bash的内建命令，它的退出码（exit status）是（0）。

如：

```sh
#!/bin/bash

while :
do
    echo "endless loop"
done
```

等价于

```sh
#!/bin/bash

while true
do
    echo "endless loop"
done
```

可以在 if/then 中作占位符：

```sh
#!/bin/bash

condition=5

if [ $condition -gt 0 ] #gt表示greater than，也就是大于，同样有-lt（小于），-eq（等于） 
then :   # 什么都不做，退出分支
else
    echo "$condition"
fi
```

#### 2.变量扩展/子串替换

在与`>`重定向操作符结合使用时，将会把一个文件清空，但是并不会修改这个文件的权限。如果之前这个文件并不存在，那么就创建这个文件。

```sh
$ : > test.sh   # 文件“test.sh”现在被清空了
# 与 cat /dev/null > test.sh 的作用相同
# 然而,这并不会产生一个新的进程, 因为“:”是一个内建命令
```

在与`>>`重定向操作符结合使用时，将不会对预先存在的目标文件 (: >> target_file)产生任何影响。如果这个文件之前并不存在，那么就创建它。

也可能用来作为注释行，但不推荐这么做。使用 # 来注释的话，将关闭剩余行的错误检查，所以可以在注释行中写任何东西。然而，使用 : 的话将不会这样。如：

```sh
$ : This is a comment that generates an error,( if [ $x -eq 3] )
```

":"还用来在 `/etc/passwd` 和 `$PATH` 变量中做分隔符，如：

```sh
$ echo $PATH
/usr/local/bin:/bin:/usr/bin:/usr/X11R6/bin:/sbin:/usr/sbin:/usr/games
```

### 八、问号`?`

#### 测试操作符

在一个双括号结构中，? 就是C语言的三元操作符，如：

```sh
$ vim test.sh
```

输入如下代码，并保存：

```sh
 #!/bin/bash

 a=10
 (( t=a<50?8:9 ))
 echo $t
```

运行测试

```sh
$ bash test.sh
8
```

### 九、美元符号`$`

#### 变量替换

前面已经用到了

```sh
$ vim test.sh
#!/bin/bash

var1=5
var2=23skidoo

echo $var1     # 5
echo $var2     # 23skidoo
```

运行测试

```sh
$ bash test.sh
5
23skidoo
```

### 十、小括号`( )`

#### 1.命令组

在括号中的命令列表，将会作为一个`子 shell `来运行。

在括号中的变量，由于是在子shell中，所以对于脚本剩下的部分是不可用的。父进程，也就是脚本本身，将不能够读取在子进程中创建的变量，也就是在子shell 中创建的变量。如：

```sh
$ vim test20.sh
```

输入代码：

```sh
#!/bin/bash

a=123
( a=321; )

echo "$a" #a的值为123而不是321，因为括号将判断为局部变量
```

运行代码：

```sh
$ bash test20.sh
a = 123
```

在圆括号中 a 变量，更像是一个局部变量。

#### 2.初始化数组

创建数组

```sh
$ vim test21.sh
```

输入代码：

```sh
#!/bin/bash

arr=(1 4 5 7 9 21)
echo ${arr[3]} # get a value of arr
```

运行代码：

```sh
$ bash test21.sh
7
```

### 十一、大括号`{ }`

#### 1.文件名扩展

复制 t.txt 的内容到 t.back 中

```sh
$ vim test22.sh
```

输入代码：

```sh
#!/bin/bash

if [ ! -w 't.txt' ];
then
    touch t.txt
fi
echo 'test text' >> t.txt
cp t.{txt,back}
```

运行代码：

```sh
$ bash test22.sh
```

查看运行结果：

```sh
$ ls
$ cat t.txt
$ cat t.back
```

> 注意： 在大括号中，不允许有空白，除非这个空白被引用或转义。

#### 2.代码块

代码块，又被称为内部组，这个结构事实上创建了一个匿名函数（一个没有名字的函数）。然而，与“标准”函数不同的是，在其中声明的变量，对于脚本其他部分的代码来说还是可见的。

```sh
$ vim test23.sh
```

输入代码：

```sh
#!/bin/bash

a=123
{ a=321; }
echo "a = $a"
```

运行代码：

```sh
$ bash test23.sh
a = 321
```

变量 a 的值被更改了。

### 十二、中括号`[ ]`

#### 1.条件测试

条件测试表达式放在[ ]中。下列练习中的-lt (less than)表示小于号。

```sh
$ vim test24.sh
```

输入代码：

```sh
#!/bin/bash

a=5
if [ $a -lt 10 ]
then
    echo "a: $a"
else
    echo 'a>=10'
fi
```

运行代码：

```sh
$ bash test24.sh
a: 5
```

双中括号`[[ ]]`也用作条件测试（判断），后面的实验会详细讲解。

​	①[[是 bash 程序语言的关键字。并不是一个命令，[[ ]] 结构比[ ]结构更加通用。在[[和]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换。

  ②支持字符串的模式匹配，使用=~操作符时甚至支持shell的正则表达式。字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[ hello == hell? ]]，结果为真。[[ ]] 中匹配字符串或通配符，不需要引号。

  ③使用[[ ... ]]条件判断结构，而不是[ ... ]，能够`防止脚本中的许多逻辑错误`。比如，&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错。比如可以直接使用`if [[ $a != 1 && $a != 2 ]]`, 如果不适用双括号, 则为`if [ $a -ne 1] && [ $a != 2 ]`或者`if [ $a -ne 1 -a $a != 2 `]。

  ④bash把双中括号中的表达式看作一个单独的元素，并返回一个退出状态码。

#### 2.数组元素

在一个array结构的上下文中，中括号用来引用数组中每个元素的编号。

```sh
$ vim test25.sh
```

输入代码：

```sh
#!/bin/bash

arr=(12 22 32)
arr[0]=10
echo ${arr[0]}
```

运行代码：

```sh
$ bash test25.sh
10
```

### 十三、尖括号 `>`

#### 重定向

`test.sh > filename` #重定向test.sh的输出到文件 filename 中。如果 filename 存在的话，那么将会被覆盖。

`test.sh &> filename` #重定向 test.sh 的 stdout（标准输出）和 stderr（标准错误）到 filename 中。

`test.sh >&2` #重定向 test.sh 的 stdout 到 stderr 中。

`test.sh >> filename` #把 test.sh 的输出追加到文件 filename 中。如果filename 不存在的话，将会被创建。

### 十四、竖线`|`

#### 管道

分析前边命令的输出，并将输出作为后边命令的输入。这是一种产生命令链的好方法。

```sh
$ vim test26.sh
```

输入代码：

```sh
#!/bin/bash

tr 'a-z' 'A-Z'
exit 0
```

现在让我们输送ls -l的输出到一个脚本中：

```sh
$ chmod 755 test26.sh
$ ls -l | ./test26.sh
```

输出的内容均变为了大写字母。

### 十五、破折号`-`

#### 1.选项，前缀

在所有的命令内如果想使用选项参数的话,前边都要加上“-”。

```sh
$ vim test27.sh
```

输入代码：

```sh
#!/bin/bash

a=5
b=5
if [ "$a" -eq "$b" ]
then
    echo "a is equal to b."
fi
```

运行代码：

```sh
$ bash test27.sh
a is equal to b.
```

#### 2.用于重定向stdin或stdout

下面脚本用于备份最后24小时当前目录下所有修改的文件.

```sh
$ vim test28.sh
```

输入代码：

```sh
#!/bin/bash

BACKUPFILE=backup-$(date +%m-%d-%Y)
# 在备份文件中嵌入时间.
archive=${1:-$BACKUPFILE}
#  如果在命令行中没有指定备份文件的文件名,
#  那么将默认使用"backup-MM-DD-YYYY.tar.gz".

tar cvf - `find . -mtime -1 -type f -print` > $archive.tar
gzip $archive.tar
echo "Directory $PWD backed up in archive file \"$archive.tar.gz\"."

exit 0
```

运行代码：

```sh
$ bash test28.sh
$ ls
```

### 十六、波浪号`~`

#### 目录

~ 表示 home 目录。

## 变量和参数

### 一、变量定义

#### 1.概念

变量的名字就是变量保存值的地方。引用变量的值就叫做变量替换。

如果 variable 是一个变量的名字，那么 $variable 就是引用这个变量的值，即这变量所包含的数据。

`$variable` 事实上只是 `${variable}` 的简写形式。在某些上下文中` $variable` 可能会引起错误，这时候你就需要用 ​`${variable} `了。

#### 2.定义变量

定义变量时，变量名不加美元符号（$，PHP语言中变量需要），如： myname="shiyanlou"

**注意**

**变量名和等号之间不能有空格**。同时，变量名的命名须遵循如下规则：

+ 首个字符必须为字母（a-z，A-Z）。
+ 中间不能有空格，可以使用下划线（_）。
+ 不能使用标点符号。
+ 不能使用bash里的关键字（可用help命令查看保留关键字）。

除了直接赋值，还可以用语句给变量赋值，如：for file in `ls /etc`

### 二、使用变量

变量名前加**美元符号**，如：

```sh
myname="shiyanlou"
echo $myname
echo ${myname}
echo ${myname}Good
echo $mynameGood

myname="miao"
echo ${myname}
```

> 加**花括号**帮助解释器识别变量的**边界**，若不加，解释器会把mynameGood当成一个变量（值为空）
>
> 推荐给所有变量加花括号
>
> 已定义的变量可以重新被定义

### 三、只读变量

使用 `readonly` 命令可以将变量定义为只读变量，<u>只读变量的值不能被改变</u>。 下面的例子尝试更改只读变量，结果报错：

```sh
#!/bin/bash
myUrl="http://www.shiyanlou.com"
readonly myUrl
myUrl="http://www.shiyanlou.com"
```

运行脚本，结果如下：

```sh
/bin/sh: NAME: This variable is read only.
```

### 四、特殊变量

#### 1.局部变量

这种变量只有在代码块或者函数中才可见。后面的实验会详细讲解。

#### 2.环境变量

这种变量将影响用户接口和 shell 的行为。

在通常情况下，每个进程都有自己的“环境”，这个环境是由一组变量组成的，这些变量中存有进程可能需要引用的信息。在这种情况下，shell 与一个一般的进程没什么区别。

#### 3.位置参数

从命令行传递到脚本的参数：0，0，1，2，2，3...

**0就是脚本文件自身的名字**，1 是第一个参数，2 是第二个参数，2是第二个参数，3 是第三个参数，然后是第四个。

9 之后的位置参数就必须用大括号括起来了，比如，9之后的位置参数就必须用大括号括起来了，比如，{10}，{11}，11，{12}。

| 位置参数 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| `$0`     | 脚本文件自身的名字                                           |
| `$#`     | 传递到脚本的参数个数                                         |
| `$*`     | 以一个单字符串显示所有向脚本传递的参数。与位置变量不同，此选项参数可超过 9个 |
| `$$`     | 脚本运行的当前进程 ID号                                      |
| `$!`     | 后台运行的最后一个进程的进程 ID号                            |
| `$@`     | 与`$*`相同,但是使用时加引号，并在引号中返回每个参数          |
| `$`      | 显示shell使用的当前选项，与 set命令功能相同                  |
| `$?`     | **显示最后命令的退出状态**。 0表示没有错误，其他任何值表明有错误。 |



#### 4.位置参数实例

这个十分重要，在我们运行一套脚本的时候，有时候是需要参数的，这里我们教大家如何获取参数

```sh
$ vim test30.sh
```

输入代码（中文皆为注释，不用输入）：

```sh
#!/bin/bash

# 作为用例, 调用这个脚本至少需要10个参数, 比如:
# bash test.sh 1 2 3 4 5 6 7 8 9 10
MINPARAMS=10

echo

echo "The name of this script is \"$0\"."

echo "The name of this script is \"`basename $0`\"."


echo

if [ -n "$1" ]              # 测试变量被引用.
then
echo "Parameter #1 is $1"  # 需要引用才能够转义"#"
fi 

if [ -n "$2" ]
then
echo "Parameter #2 is $2"
fi 

if [ -n "${10}" ]  # 大于$9的参数必须用{}括起来.
then
echo "Parameter #10 is ${10}"
fi 

echo "-----------------------------------"
echo "All the command-line parameters are: "$*""

if [ $# -lt "$MINPARAMS" ]
then
 echo
 echo "This script needs at least $MINPARAMS command-line arguments!"
fi  

echo

exit 0
```

运行代码：

```sh
$ bash test30.sh 1 2 10


The name of this script is "test.sh".
The name of this script is "test.sh".

Parameter #1 is 1
Parameter #2 is 2
-----------------------------------
All the command-line parameters are: 1 2 10

This script needs at least 10 command-line arguments!
```

## 基本运算符

### 一、算数运算符

![5-1-1](../.img/document-uid8797labid3895timestamp1554172034738.png)

```sh
$vim test.sh
#!/bin/bash

a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a == b"
fi
if [ $a != $b ]
then
   echo "a != b"
fi
```

运行

```sh
$bash test.sh
a + b : 30
a - b : -10
a * b : 200
b / a : 2
b % a : 0
a != b
```

> + 原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 `awk` 和 `expr`，`expr` 最常用。
> + `expr` 是一款表达式计算工具，使用它能完成表达式的求值操作。
> + 注意使用的反引号（esc键下边）
> + 表达式和运算符之间要有空格`$a + $b`写成`$a+$b`不行
> + 条件表达式要放在方括号之间，并且要有空格`[ $a == $b ]`写成`[$a==$b]`不行
> + 乘号（`*`）前边必须加反斜杠（`\`)才能实现乘法运算

### 二、关系运算符

关系运算符`只支持数字`，不支持字符串，除非字符串的值是数字。

| 运算符 | 说明                                                 |
| ------ | ---------------------------------------------------- |
| -eq    | 检测两个数是否相对，`相等`返回true                   |
| -ne    | 检测两个数是否相对，`不相等`返回true                 |
| -gt    | 检测左边的数是否`大于`右边的，如果是，则返回true     |
| -lt    | 检测左边的数是否`小于`右边的，如果是，则返回true     |
| -ge    | 检测左边的数是否`大于等于`右边的，如果是，则返回true |
| -le    | 检测左边的数是否`小于等于`右边的，如果是，则返回true |

实例

```sh
vim test2.sh
#!/bin/bash

a=10
b=20

if [ $a -eq $b ]
then
   echo "$a -eq $b : a == b"
else
   echo "$a -eq $b: a != b"
fi
```

运行

```sh
$bash test2.sh
10 -eq 20: a != b
```

### 三、逻辑运算符

![5-3-1](../.img/document-uid8797labid3895timestamp1554172035321.png)

实例

```sh
#!/bin/bash
a=10
b=20

if [[ $a -lt 100 && $b -gt 100 ]]
then
   echo "return true"
else
   echo "return false"
fi

if [[ $a -lt 100 || $b -gt 100 ]]
then
   echo "return true"
else
   echo "return false"
fi
```

结果

```sh
return false
return true
```

### 四、字符串运算符

| 运算符 | 说明                                   |
| ------ | -------------------------------------- |
| `=`    | 检测两个字符串是否相等，相等返回true   |
| `!=`   | 检测两个字符串是否相等，不相等返回true |
| `-z`   | 检测字符串长度是否为0，为0返回true     |
| `-n`   | 检测字符串长度是否为0，不为0返回true   |
| `str`  | 检测字符串是否为空，不为空返回true     |

```sh
#!/bin/bash

a="abc"
b="efg"

if [ $a = $b ]
then
   echo "$a = $b : a == b"
else
   echo "$a = $b: a != b"
fi
if [ -n $a ]
then
   echo "-n $a : The string length is not 0"
else
   echo "-n $a : The string length is  0"
fi
if [ $a ]
then
   echo "$a : The string is not empty"
else
   echo "$a : The string is empty"
fi
```

结果

```sh
abc = efg: a != b
-n abc : The string length is not 0
abc : The string is not empty
```

### 五、文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：

| 操作符    | 说明                                                         | 举例                      |
| :-------- | :----------------------------------------------------------- | :------------------------ |
| -b file   | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file   | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] 返回 false。 |
| -d file   | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file   | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file   | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file   | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file   | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file   | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| `-r` file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| `-w` file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| `-x` file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| `-s` file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file   | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |

其他检查符：

+ **-S**: 判断某文件是否 socket。
+ **-L**: 检测文件是否存在并且是一个符号链接。

实例

```sh
#!/bin/bash

file="/home/shiyanlou/test.sh"
if [ -r $file ]
then
   echo "The file is readable"
else
   echo "The file is not readable"
fi
if [ -e $file ]
then
   echo "File exists"
else
   echo "File not exists"
fi
```

结果

```
The file is readable
File exists
```

### 挑战:矩形的面积和周长

#### 已知条件

矩形的长 a=3，宽 b=2

#### 目标

创建一个 Area.sh，能够计算此矩形的面积，输出面积的值

创建一个 Cum.sh,能够计算此矩形的周长，输出周长的值

#### 注意

+ 文件名一定要一致，以便于验证结果
+ 文件创建在 `/home/shiyanlou/` 下

#### 参考代码

**注意：请务必先独立思考获得 PASS 之后再查看参考代码，直接拷贝代码收获不大**

此题解法不唯一，这里只是给出其中一种作为参考。

`/home/shiyanlou/Area.sh` 的参考代码：

```bash
#!/bin/bash
a=3
b=2
echo `expr $a \* $b`
```

`/home/shiyanlou/Cum.sh` 的参考代码：

```bash
#!/bin/bash
a=3
b=2
c=`expr $a + $b`
echo `expr $c \* 2`
```

#### 浮点运算，比如实现求圆的面积和周长。

> `expr` 只能用于整数计算，可以使用 `bc` 或者 `awk` 进行浮点数运算。

```bash
#!/bin/bash

raduis=2.4

pi=3.14159

girth=$(echo "scale=4; 3.14 * 2 * $raduis" | bc)

area=$(echo "scale=4; 3.14 * $raduis * $raduis" | bc)

echo "girth=$girth"

echo "area=$area"
```

> 以上代码如果想在环境中运行，需要先安装 `bc`。

```bash
$ sudo apt-get update
$ sudo apt-get install bc
```

## 流程控制

### 一、if else

和Java、PHP等语言不一样，sh的流程控制不可为空

在sh/bash里可不能这么写，如果else分支没有语句执行，就不要写这个else。

#### 1.if

if 语句语法格式：

```sh
if condition
then
    command1 
    command2
    ...
    commandN 
fi
```

#### 2.if else

if else 语法格式：

```sh
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi
```

if-elif-else 语法格式：

```sh
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

以下实例判断两个变量是否相等：

```sh
a=10
b=20
if [ $a == $b ]
then
   echo "a == b"
elif [ $a -gt $b ]
then
   echo "a > b"
elif [ $a -lt $b ]
then
   echo "a < b"
else
   echo "Ineligible"
fi
```

输出结果：

```
a < b
```

`if else`语句经常与`test`命令结合使用

```sh
num1=$[2*3]
num2=$[1+5]
if test $[num1] -eq $[num2]
then
    echo 'Two numbers are equal!'
else
    echo 'The two numbers are not equal!'
fi
```

输出结果：

```
Two numbers are equal!
```

### 二、for 循环

for循环一般格式为：

```sh
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

例如，顺序输出当前列表中的数字：

```sh
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

输出结果：

```sh
The value is: 1
The value is: 2
The value is: 3
The value is: 4
The value is: 5
```

顺序输出字符串中的字符：

```sh
for str in This is a string
do
    echo $str
done
```

输出结果：

```sh
This
is
a
string
```

### 三、while 语句

while循环用于不断执行一系列命令，也用于从输入文件中读取数据；命令通常为测试条件。其格式为：

```sh
while condition
do
    command
done
```

```sh
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

运行脚本，输出：

```
1
2
3
4
5
```

> + 如果int小于等于5，那么条件返回真。int从1开始，每次循环处理时，int加1。运行上述脚本，返回数字1到5，然后终止。
> + 使用了`Bash let` 命令，<u>***它用于执行一个或多个表达式***</u>，变量计算中不需要加上 $ 来表示变量

while循环可用于`读取键盘信息`。下面的例子中，输入信息被设置为变量MAN，按结束循环。

```sh
echo 'press <CTRL-D> exit'
echo -n 'Who do you think is the most handsome: '
while read MAN
do
    echo "Yes！$MAN is really handsome"
done
```

### 四、无限循环

无限循环语法格式：

```sh
while :
do
    command
done
或者
while true
do
    command
done
```

或者

```sh
for (( ; ; ))
```

### 五、until循环

until循环执行一系列命令直至条件为真时停止。 until循环与while循环在处理方式上刚好相反。 一般while循环优于until循环，但在某些时候—也只是极少数情况下，until循环更加有用。 until 语法格式:

```sh
until condition
do
    command
done
```

条件可为任意测试条件，测试发生在循环末尾，因此循环至少执行一次—请注意这一点。

### 六、 case

Shell case语句为多选择语句。可以用case语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。case语句格式如下：

```sh
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```

> + 取值后面必须为单词in，每一模式必须以右括号`)`结束。取值可以为变量或常数。匹配发现取值符合某一模式后，其间所有命令开始执行直至 `;;` break。
> + 取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 `*` 捕获该值`default`，再执行后面的命令。
> + 需要`esac`（就是case反过来）作为结束标记，每个case分支用右圆括号，用两个分号表示break。

下面的脚本提示输入1到4，与每一种模式进行匹配：

```sh
echo 'Enter a number between 1 and 4:'
echo 'The number you entered is:'
read aNum
case $aNum in
    1)  echo 'You have chosen 1'
    ;;
    2)  echo 'You have chosen 2'
    ;;
    3)  echo 'You have chosen 3'
    ;;
    4)  echo 'You have chosen 4'
    ;;
    *)  echo 'You did not enter a number between 1 and 4'
    ;;
esac
```

输入不同的内容，会有不同的结果，例如：

```
Enter a number between 1 and 4:
The number you entered is:
3
You have chosen 3
```

### 七、跳出循环

在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell使用两个命令来实现该功能：break和continue。

#### break命令

break命令允许`跳出该层所有循环`（终止执行后面的所有循环）。 下面的例子中，脚本进入死循环直至用户输入数字大于5。要跳出这个循环，返回到shell提示符下，需要使用break命令。

```sh
#!/bin/bash
while :
do
    echo -n "Enter a number between 1 and 5:"
    read aNum
    case $aNum in
        1|2|3|4|5) echo "The number you entered is $aNum!"
        ;;
        *) echo "The number you entered is not between 1 and 5! game over!"
            break
        ;;
    esac
done
```

执行以上代码，输出结果为：

```
Enter a number between 1 and 5:3
The number you entered is 3!
Enter a number between 1 and 5:7
The number you entered is not between 1 and 5! game over!
```

#### continue

continue命令与break命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。 对上面的例子进行修改：

```sh
#!/bin/bash
while :
do
    echo -n "Enter a number between 1 and 5: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "The number you entered is $aNum!"
        ;;
        *) echo "The number you entered is not between 1 and 5!"
            continue
            echo "game over"
        ;;
    esac
done
```

运行代码发现，当输入大于5的数字时，该例中的循环不会结束，语句 `echo "Game is over!"` 永远不会被执行。

### 实战

####  题目一

写一个脚本

(1) 提示用户输入一个字符串；

(2) 判断：

如果输入的是quit，则退出脚本；

否则，则显示其输入的字符串内容；

```sh
#!/bin/bash

read -t 10 -p "Please enter string: " inputStr
case $inputStr in
quit)
  exit 1
  ;;
*)
  echo "$inputStr"
  ;;
esac
```

#### 题目二

有一个８升的瓶子装满水，还有一个５升的空瓶子和一个３升的空瓶子。要求将水分成两个４升。

运行脚本之后要生产类似这样的解决方案：

```
Your containers: 8     5     3

Solution1 step0: 8-->0-->0
Solution1 step1: 3-->5-->0
Solution1 step2: 3-->2-->3
Solution1 step3: 6-->2-->0
Solution1 step4: 6-->0-->2
Solution1 step5: 1-->5-->2
Solution1 step6: 1-->4-->3
Solution1 step7: 4-->4-->0
```

> 提示：上述题目的解法不唯一，你只需要通过流程控制来实现其中的一种就可以了。（注意题中所给的解法，它其实是重复进行的。只有 step3 -> step4 是需要单独注意的。）

#### 题目三

三角输出

编写bash脚本输出如图的三角

![6-10-1](../.img/document-uid8797labid3563timestamp1505359604667.png)

```bash
#!/bin/bash
for((i=1;i<=5;i++))
do

  spaceNum=$((5-$i))
  num=$((2*$i-1))

  for ((j=1; j<=$spaceNum; j++))
  do
    echo -n ' '
  done

  for ((k=1; k<=$num; k++))
  do
    echo -n '*'
  done

  for ((l=1; j<=$spaceNum; l++))
  do
    echo -n ' '
  done

  echo ''

done
```





### 挑战:最大值

##### 目标

新建一个 test.sh，判断 8 4 5 三个数字的最大值

##### 输出

最大值

##### 提示

+ 文件创建在`/home/shiyanlou/`下

##### 参考代码

**注意：请务必先独立思考获得 PASS 之后再查看参考代码，直接拷贝代码收获不大**

此题解法不唯一，这里只是给出其中一种作为参考。

`/home/shiyanlou/test.sh` 参考代码：

```sh
#!/bin/bash
max=0
a=8
b=4
c=5
for i in $a $b $c
do 
  if [ $i -gt $max ]
  then 
    max=$i
  fi
done
echo $max
```

### 挑战:偶数之和

##### 目标

新建 test.sh 求 100 以内所有偶数之和

##### 输出

和的值

##### 提示

文件创建在 `/home/shiyanlou/` 下

##### 参考代码

**注意：请务必先独立思考获得 PASS 之后再查看参考代码，直接拷贝代码收获不大**

此题解法不唯一，这里只是给出其中一种作为参考。

`/home/shiyanlou/test.sh` 的参考代码：

```bash
#!/bin/bash
cnt=0
sum=0
for cnt in `seq 2 2 100`
do
  sum=$((cnt+sum))
done 

echo $sum
```

`seq 2 2 100` 表示列出 1 到 100 的所有偶数

## 函数

### 一、函数定义

shell中函数的定义格式如下：

```sh
function  funname(){
    action...
    return int;
}
```

> + 可以带function fun() 定义，也`可以直接fun() 定义`，不带任何参数。
> + 参数返回，可以显示加：`return 返回`，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255)

下面的例子定义了一个函数并进行调用：

```sh
#!/bin/bash

demoFun(){
    echo "This is my first shell function!"
}
echo "-----Execution-----"
demoFun
echo "-----Finished-----"


Output the result：
-----Execution-----
This is my first shell function!
-----Finished-----
```

下面定义一个带有return语句的函数：

```sh
#!/bin/bash
funWithReturn(){
    echo "This function will add the two numbers of the input..."
    echo "Enter the first number: "
    read aNum
    echo "Enter the second number: "
    read anotherNum
    echo "The two numbers are $aNum and $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "The sum of the two numbers entered is $? !"
```

输出类似下面：

```
This function will add the two numbers of the input...
Enter the first number: 
1
Enter the second number: 
2
The two numbers are 1 and  2 !
The sum of the two numbers entered is 3 !
```

> + 函数返回值在调用该函数后通过 $? 来获得
> + 所有函数在使用前必须定义。

### 二、函数参数

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 n 的形式来获取参数的值，例如，`$n`的形式来获取参数的值，例如，`$1`表示第一个参数，`$2`表示第二个参数... 带参数的函数示例：

```sh
#!/bin/bash
funWithParam(){
    echo "The first parameter is $1 !"
    echo "The second parameter is $2 !"
    echo "The tenth parameter is $10 !"
    echo "The tenth parameter is ${10} !"
    echo "The eleventh parameter is ${11} !"
    echo "The total number of parameters is $# !"
    echo "Outputs all parameters as a string $* !"
    
    for var in $*
    do
      echo $var
    done
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

输出结果：

```
The first parameter is 1 !
The second parameter is 2 !
The tenth parameter is 10 !
The tenth parameter is 34 !
The eleventh parameter is 73 !
The total number of parameters is 11 !
Outputs all parameters as a string 1 2 3 4 5 6 7 8 9 34 73 !
```



> **注意**: 10 不能获取第十个参数，获取第十个参数需要10不能获取第十个参数，获取第十个参数需要{10}。当n>=10时，需要使用`${n}`来获取参数。