## channel

`channel` 是`goroutine` 之间互相通讯的东西。类似我们 Unix 上的管道（可以在进程间传递消息），用来 `goroutine` 之间发消息和接收消息。其实，就是在做 `goroutine` 之间的内存共享。`channel` 是类型相关的，也就是说一个 `channel` 只能传递一种类型的值，这个类型需要在 `channel` 声明时指定。

#### 声明与初始化

`channel` 的一般声明形式：**var** chanName **chan** **ElementType**。

与普通变量的声明不同的是在类型前面加了 `channel` 关键字，`ElementType` 则指定了这个 `channel` 所能传递的元素类型。示例：

```go
var a chan int //声明一个传递元素类型为int的channel
var b chan float64
var c chan string
```

初始化一个 `channel` 也非常简单，直接使用 Go 语言内置的 `make()` 函数，示例：

```go
a := make(chan int) //初始化一个int型的名为a的channel
b := make(chan float64)
c := make(chan string)
```

`channel` 最频繁的操作就是写入和读取，这两个操作也非常简单，示例：

```go
a := make(chan int)
a <- 1  //将数据写入channel
z := <-a  //从channel中读取数据
```

#### 优雅通讯，让主协程等待

##### 单个协程等待

```go
func add(a, b int, ch chan int) {
	c := a + b
	fmt.Printf("%d + %d = %d\n", a, b, c)
	ch <- 1
}

func main() {
	c := make(chan int)
	go add(1, 2, c)
	<-c
}
```



##### 多个协程等待

我们首先定义了一个包含 10 个通道类型的切片 `chs`，并把切片中的每个通道分配给 10 个不同的协程。在每个协程的 `add()` 函数业务逻辑完成后，我们通过 `ch <- 1` 语句向对应的通道中发送一个数据。在所有的协程启动完成后，我们再通过 `<-ch` 语句从通道切片 `chs` 中依次接收数据（不对结果做任何处理，相当于写入通道的数据只是个标识而已，表示这个通道所属的协程逻辑执行完毕），直到所有通道数据接收完毕，然后打印主程序耗时并退出。

```go
func add(a, b int, ch chan int) {
	c := a + b
	fmt.Printf("%d + %d = %d\n", a, b, c)
	ch <- 1
}

func main() {
    // 大小为10的切片通道 不是缓冲通道！
	chanSlices := make([]chan int, 10)

	for i := 0; i < 10; i++ {
		chanSlices[i] = make(chan int)
		go add(1, i, chanSlices[i])
	}
    
    // 等待读取，阻塞直到主协程退出
	for _, ch := range chanSlices {
		<-ch
	}
}
```



