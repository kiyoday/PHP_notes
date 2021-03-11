## select

`select` 用于处理异步 IO 问题，它的语法与 `switch` 非常类似。由 `select` 开始一个新的选择块，每个选择条件由 `case` 语句来描述，并且每个 `case` 语句里必须是一个 `channel` 操作。它既可以用于 `channel` 的数据接收，也可以用于 `channel` 的数据发送。如果 `select` 的多个分支都满足条件，则会随机的选取其中一个满足条件的分支。

**每个 `case` 语句都必须是一个面向通道的操作**

```go
select { 
    case <-chan1:
        // 如果从 chan1 通道成功接收数据，则执行该分支代码
    case chan2 <- 1:
        // 如果成功向 chan2 通道成功发送数据，则执行该分支代码 
    default:
        // 如果上面都没有成功，则进入 default 分支处理流程 
}
```

> 注：Go 语言的 `select` 语句借鉴自 Unix 的 `select()` 函数，在 Unix 中，可以通过调用 `select()` 函数来监控一系列的文件句柄，一旦其中一个文件句柄发生了 IO 动作，该 `select()` 调用就会被返回（C 语言中就是这么做的），后来该机制也被用于实现高并发的 Socket 服务器程序。Go 语言直接在语言级别支持 `select` 关键字，用于处理并发编程中通道之间异步 IO 通信问题。



需要注意的是这两个 `case` 的执行不是 `if...else...` 那种先后关系，

1. 而是会并发执行，然后 `select` 会选择先操作成功返回的那个 `case` 分支去执行，
2. 如果两者同时返回，则**随机选择一个执行**，
3. 如果这两者都没有返回，则进入 `default` 分支，这里也不会出现阻塞
4. 但是如果没有 `default` 语句，则会阻塞直到某个通道操作成功。





新建源文件 `channel.go`，输入以下代码：

```go
package main
import "time"
import "fmt"
func main() {
    c1 := make(chan string)
    c2 := make(chan string)
    go func() {
        time.Sleep(time.Second * 1)
        c1 <- "one"
    }()
    go func() {
        time.Sleep(time.Second * 2)
        c2 <- "two"
    }()
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-c1:
            fmt.Println("received", msg1)
        case msg2 := <-c2:
            fmt.Println("received", msg2)
        }
    }
}
```

以上代码先初始化两个 `channel` c1 和 c2，然后开启两个 `goroutine` 分别往 c1 和 c2 写入数据，再通过 `select` 监听两个 `channel`，从中读取数据并输出。

运行结果如下：

```bash
$ go run channel.go
received one
received two
```