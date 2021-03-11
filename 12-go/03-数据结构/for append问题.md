# 昨天那个在for循环里append元素的同事，今天还在么？

```go
package main

import "fmt"

func main() {
    s := []int{1,2,3,4,5}
    for _, v:=range s {
        s =append(s, v)
        fmt.Printf("len(s)=%v\n",len(s))
    }
}
```

**这个代码会造成死循环吗？**

> **不会死循环**，`for range`其实是`golang`的`语法糖`:
>
> 1. 在循环开始前会获取切片的长度 `len(切片)`，
> 2. 然后再执行`len(切片)`次数的循环。
>

## nil切片和空切片一不一样?


```go
package main

import (
    "fmt"
    "reflect"
    "unsafe"
)

func main() {

    var s1 []int
    s2 := make([]int,0)
    s4 := make([]int,0)

    fmt.Printf("s1 pointer:%+v, s2 pointer:%+v, s4 pointer:%+v, \n", *(*reflect.SliceHeader)(unsafe.Pointer(&s1)),*(*reflect.SliceHeader)(unsafe.Pointer(&s2)),*(*reflect.SliceHeader)(unsafe.Pointer(&s4)))
    
    fmt.Printf("%v\n", (*(*reflect.SliceHeader)(unsafe.Pointer(&s1))).Data==(*(*reflect.SliceHeader)(unsafe.Pointer(&s2))).Data)
    
    fmt.Printf("%v\n", (*(*reflect.SliceHeader)(unsafe.Pointer(&s2))).Data==(*(*reflect.SliceHeader)(unsafe.Pointer(&s4))).Data)
}
```

**nil切片和空切片指向的地址一样吗？这个代码会输出什么？**

### 怎么答

+ **nil切片和空切片指向的地址不一样。**
+ **nil空切片引用数组指针`地址为0`（无指向任何实际地址）**
+ **空切片的引用数组指针`地址是有的，且固定为一个值`**

```go
s1 pointer:{Data:0 Len:0 Cap:0}, s2 pointer:{Data:824634207952 Len:0 Cap:0}, s4 pointer:{Data:824634207952 Len:0 Cap:0}, 
false //nil切片和空切片指向的数组地址不一样
true  //两个空切片指向的数组地址是一样的，都是824634207952
```