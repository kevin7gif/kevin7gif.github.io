### 通道

Go 语言之所以开始流行起来，很大一部分原因是因为它自带的并发机制。如果说 goroutine 是 Go语言程序的并发体的话，那么 channel（信道） 就是 它们之间的通信机制。channel，是一个可以让一个goroutine 与另一个 goroutine 传输信息的通道，我把他叫做信道，也有人将其翻译成通道，二者都是一个概念。

信道，就是一个管道，连接多个goroutine程序 ，它是一种队列式的数据结构，遵循先入先出的规则。

**“不要通过共享内存来通信，而应该通过通信来共享内存”**



### 信道声明和定义

```
每个信道都只能传递一种数据类型的数据，所以在你声明的时候，你得指定数据类型（string int 等等）

var 信道实例 chan 信道类型
声明后的信道，其零值是nil，无法直接使用，必须配合make函进行初始化。

信道实例 = make(chan 信道类型)
亦或者，上面两行可以合并成一句，以下我都使用这样的方式进行信道的声明

信道实例 := make(chan 信道类型)
假如我要创建一个可以传输int类型的信道，可以这样子写。

// 定义信道
pipline := make(chan int)
信道的数据操作，无非就两种：发送数据与读取数据

// 往信道中发送数据
pipline<- 200

// 从信道中取出数据，并赋值给mydata
mydata := <-pipline
信道用完了，可以对其进行关闭，避免有人一直在等待。但是你关闭信道后，接收方仍然可以从信道中取到数据，只是接收到的会永远是 0。

close(pipline)
对一个已关闭的信道再关闭，是会报错的。所以我们还要学会，如何判断一个信道是否被关闭？

当从信道中读取数据时，可以有多个返回值，其中第二个可以表示 信道是否被关闭，如果已经被关闭，ok 为 false，若还没被关闭，ok 为true。

x, ok := <-pipline
```



### 遍历信道

```go
import "fmt"

func fibonacci(mychan chan int) {
    n := cap(mychan)
    x, y := 1, 1
    for i := 0; i < n; i++ {
        mychan <- x
        x, y = y, x+y
    }
    // 记得 close 信道
    // 不然主函数中遍历完并不会结束，而是会阻塞。
    close(mychan)
}

func main() {
    pipline := make(chan int, 10)

    go fibonacci(pipline)

    for k := range pipline {
        fmt.Println(k)
    }
}
```





### 信道容量与长度(缓冲通道)

一般创建信道都是使用 make 函数，make 函数接收两个参数

- 第一个参数：必填，指定信道类型
- 第二个参数：选填，不填默认为0，指定信道的**容量**（可缓存多少数据）

对于信道的容量，很重要，这里要多说几点：

- 当容量为0时，说明信道中不能存放数据，在发送数据时，必须要求立马有人接收，否则会报错。此时的信道称之为**无缓冲信道**。
- 当容量为1时，说明信道只能缓存一个数据，若信道中已有一个数据，此时再往里发送数据，会造成程序阻塞。 利用这点可以利用信道来做锁。
- 当容量大于1时，信道中可以存放多个数据，可以用于多个协程之间的通信管道，共享资源。

至此我们知道，信道就是一个容器。

若将它比做一个纸箱子

- 它可以装10本书，代表其容量为10
- 当前只装了1本书，代表其当前长度为1

信道的容量，可以使用 cap 函数获取 ，而信道的长度，可以使用 len 长度获取。

![image-20221215172210902](F:\软件\Typora\菜狗带俩娃\GO\image-20221215172210902.png)



![image-20221215172245586](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221215172245586.png)



```go
package main

import "fmt"

func main() {
    pipline := make(chan int, 10)
    fmt.Printf("信道可缓冲 %d 个数据\n", cap(pipline))
    pipline<- 1
    fmt.Printf("信道中当前有 %d 个数据", len(pipline))
}
//输出如下

信道可缓冲 10 个数据
信道中当前有 1 个数据
```



```go
ch3 := make(chan string,4)
go sendData(ch3)
for{
    v,ok := <-ch3
    if !ok{
        fmt.Println("读完了。。")
        break
    }
    fmt.Println("读取的数据是：",v)
}
fmt.Println("main。。over。。")

func snedData(ch chan string){
    for i:=0;i<10;i++{
        ch<-"数据"+strconv.Itoa(i)
        fmt.Printf("子协程写入第%d个数据",i)
    }
    close(ch)
}
```





### 定向通道

> 信道只读或者只写

> `<-chan` 表示这个信道，只能从里发出数据，对于程序来说就是只读

> `chan<-` 表示这个信道，只能从外面接收数据，对于程序来说就是只写

```go
//双向通道
ch1 := make(chan string)
done := make(chan bool)
go sendData(ch1,done)

data := <-ch1//读取数据
fmt.Println("子协程传来：",data)

ch1<-"北京"//发送数据

<-done//用来让主函数不在子协程还没执行完就结束
fmt.Println("main。。over。。。")

func sendData(ch1 chan string,done chan bool){
    ch1<-"西安"//发送数据
    
    data := <-ch1//读取数据
    fmt.Println("主协程传来：",data)
    
    done<-true//在子协程执行完之后再将true传给done，让主函数继续执行
}
```

创建通道时，都默认创建双向通道，因为创建单向通道是没有意义的。信道本身就是为了传输数据而存在的，如果只有接收者或者只有发送者，那信道就变成了只入不出或者只出不入了，没什么用。所以只读信道和只写信道，缺一不可。

当然了，若你往一个只读信道中写入数据 ，或者从一个只写信道中读取数据 ，都会出错。

```go
//单向通道

ch1 := make(chan int)//双向，可读可写
ch2 := make(chan <- int)//单向，只能写
ch3 := make(<- chan int)//单向，只能读

ch1 <- 100
data := <-ch1//可以执行

ch2 <- 10
data1 := <-ch2//不能执行，ch2通道只能写数据，不能读数据

go fun1(ch1)
data := <-ch1
fmt.Println("fun1函数中写出的数据是：",data)

go fun2(ch1)
ch1 <- 200


func fun1(ch chan <-int){//只能写数据
    ch <- 100
    fmt.Println("fun1函数结束")
}

func fun2(ch <- chan int){//只能读数据
    data := <-ch
    fmt.Println("从ch中读取的数据是：",data)
}
```





### time包

> 主要是定时器，标准库中的Timer让用户可以自定义自己的超时逻辑，尤其在应对select处理多个channel的超时、单channel读写的超时等
>
> Timer是一次性的时间触发事件，这点与Ticker不同，Ticker是按一定时间间隔持续触发时间事件

创建方法

> t := time.NewTimer(d)
>
> t := time.AfterFunc(d,f)
>
> c := time.After(d)

三种方式原理相同，有三个要素

> 定时时间：就是d
>
> 触发动作：就是f
>
> 时间channel：就是t.c



#### time.NewTimer(d)

> 创建一个新的计时器，该计时器将在其通道上至少持续d之后发送当前时间

```go
timer := time.NewTimer(3*time.Second) //定时为三秒
fmt.Println("%T\n",timer)  //*time.Timer
fmt.Println(time.Now()) //打印当前时间

//此处会等待channel中的数据，阻塞三秒
ch2 := timer.C
fmt.Println(<-ch2)//打印三秒之后的时间
```



#### time.After(d)

> 在等待持续时间之后，然后在返回的通道上发送当前时间，相当于NewTimer(d).C

```go
func After(d Duration) <-chan Time

ch := tie.After(3*time.Second)
fmt.Printf("%T\n",ch) // <-chan time.Time
fmt.Println(time.Now()) //2022-12-12 18：00：30

//从该通道中获取数据，也会阻塞三秒
time2 := <-ch
fmt.Println(time2) //2022-12-12 18：00：33
```





### select语句

> select是GO中的一个控制结构，类似于switch语句，但是select会随机执行一个可运行的case，如果没有case可运行，就会阻塞，直到有case可运行

说明：

每个case都必须是一个channel

所有channel表达式都会被求值

所有被发送的表达式都会被求值

**如果有多个case都可以运行，select会随机公平地选出一个执行，其他不会执行**

否则：

如果有default语句，执行该语句

如果没有default语句，select会被阻塞，直到某个通信可以运行，GO不会重新对channel或值进行求值

```go
ch1 := make(chan int)
ch2 := make(chan int)

go func(){
    ch1 <- 100
}()
go func(){
    ch2 <- 10
}
select{
    case num1 := <- ch1:
    fmt.Println("ch1中读取的数据：",num1)
    case num2,ok := <- ch2:
    if ok{
        fmt.Println("ch2中读取的数据：",num2)
    }else{
        fmt.Println("ch2通道已经关闭")
    }
    default:
    fmt.Println("无满足条件")
}
```





### CSP并发模型

> 通信顺序进程，用于描述两个独立的并发实体通过共享的通讯channel进行通信的并发模型

GO语言的CSP模型由Goroutine和channel实现

Goroutine和channel是GO语言并发编程的两大基石，Goroutine用于执行并发任务，channel用于goroutine之间的同步、通信

channel在goroutine间架起了一条管道，在管道里传输数据，实现goroutine间的通信，由于它是线程安全的，所以用起来很方便，channel还提供了“先进先出“的特性，能影响goroutine的阻塞和唤醒

Goroutine是实际并发执行的实体，他底层是使用协程实现并发，协程是一种运行在用户态的用户线程，go底层选用协程的原因是：

用户空间避免了内核态和用户态的切换导致的成本

可以由语言和框架进行调度

更小的栈空间允许创建大量的实例