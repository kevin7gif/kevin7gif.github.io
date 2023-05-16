### 切片

数组：存储一组相同数据类型的数据结构（定长）

切片：同数组类似，也叫做变长数组或动态数组（变长）

是一个**引用类型**的容器，指向了一个底层数组，切片本身不存储数据，都是由底层数组来存储数据，故修改切片就是修改这个数组中的数据，向切片添加数据时，若没有超过容量，直接添加，若超过容量则**自动扩容（成倍增长）**，切片一旦扩容就重新指向一个新的底层数组（数组的**地址**发生改变）

声明

> var 切片名 [ ] 切片类型

> 切片名 := [ ] 切片类型 { 元素 }  //变长

### 用make()函数创建切片

> 切片名 :=  make([ ],切片类型，n，m)

> 第一个参数：类型    slice、map、chan

> 第二个参数：长度len，实际存储元素的数量

> 第三个参数：容量map，最多能存储元素的数量   //即便存入的数据超出容量最大值也无所谓，切片会自动扩容(成倍增长)



### 用append函数向切片内赋值

> silce = append( silce, elem1, elem2) //elem为添加进来的元素
>
> silce = append(silce, anothersilce...) 
>
> 

```go
package main

import "fmt"

func main() {
	//数组，定长
	arr := [4]int{1, 2, 3, 4}
	fmt.Println(arr)
	//切片
	var s1 []int
	fmt.Println(s1)

	s2 := []int{1, 2, 3, 4} //变长
	fmt.Println(s2)
	fmt.Print("%T,%T\n", arr, s2) //[4]int，[]int

	//用make函数创建切片
	s3 := make([]int, 3, 8)
	fmt.Println(s3)
	fmt.Print("容量：%d,长度：%d", cap(s3), len(s3))
	s3[0] = 1
	s3[1] = 2
	s3[2] = 3
	fmt.Println(s3)
	//fmt.Println(s3[3]) //报错，因为该切片实际能访问的最大下标为2

	//用append函数向切片写入数据
	//第一种写法
	s4 := make([]int, 0, 5)
	fmt.Println(s4)
	s4 = append(s4, 1, 2)
	fmt.Println(s4)
	s4 = append(s4, 3, 4, 5, 6)
	fmt.Println(s4)

	//第二种写法，将一个切片的元素放到另一个切片
	s4 = append(s4, s3...)
	fmt.Println(s4)

	//遍历切片
	for i := 0; i < len(s4); i++ {
		fmt.Println(s4[i])
	}

	for index, value := range s4 {
		fmt.Printf("%d,%d\n", index, value)
	}

}

```



### 在已有的数组上创建silce

> slice :=  arr[ start : end ]
>
> 切片中的数据：[ start : end )
>
> 

从已有的数组上直接创建切片，该切片的底层数组就是当前的数组

长度是从start到end切割的数据量

容量是从start到数组的末尾



注：当要向切片中添加到数据超出了切片的容量时，该切片会重新指向一个底层数组，先将之前的数据拷贝到新的底层数组，之后在将要添加的数据添加进来

```go
a := [10]int{1,2,3,4,5,6,7,8,9,10}
s1 := a[:5]    //len:5,cap:10
s2 := a[3:8]   //len:5,cap:7
s3 := a[5:]    //len:5,cap:5
s4 := a[:]     //len:10,cap:10
s1 = append(s1,2,2,2,2,2)   //此时存储的数据超出容量，s1指向的底层数组发生变化
```



数组是值类型，传递的是数据

切片是引用类型，传递到是数据的地址，多个变量指向同一块内存地址

```go 
a1 := [4]int{1,2,3,4}
a2 := a1
fmt.println(a1,a2) //a1和a2一样
a1[0]=100
fmt.println(a1,a2) //a1为[100,2,3,4],a2为[1,2,3,4]

s1 :=[]int{1,2,3,4}
s2 := s1          
fmt.println(s1,s2) //s1和s2一样
s1[0]=100
fmt.println(s1,s2)//s1为[100,2,3,4],s2为[100,2,3,4]
```



深拷贝：拷贝的是数据本身。值类型的数据默认都是深拷贝：int  float  string  bool   struct   **array**

浅拷贝：拷贝的是数据的地址，多个变量指向同一块内存。引用类型的数据默认都是浅拷贝：slice   map



copy()函数：将一个切片拷贝到另一个切片

copy( dst, src )    dst：目标切片，src：源切片
