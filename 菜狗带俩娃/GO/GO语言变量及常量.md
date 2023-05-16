### 变量初始化

1. var 变量名  变量类型 =表达式

2. var 变量名=表达式

3. 变量名：=表达式      （变量类型由编辑器自己推断）

4.  var{

   ​      变量名  变量类型 = 值

   ​	  变量名  变量类型 = 值

   ​		}

注意事项

1.变量必须先定义再使用

2. go语言是静态语言，变量的类型必须和赋值的类型一致
3. 变量名不能冲突（同一个作用域内）
4. 使用简短定义方式，左边的变量名至少有一个是新的
5. 简短定义方式不能定义全局变量
6. 变量的零值是默认值
7. 变量定义了就得使用，否则无法通过编译

```go
package main

import "fmt"

var a = 100
var b int = 1000

// c:=1000  //不能用简短定义方式定义全局变量
func main() {

	var num int
	num = 100
	fmt.Println("num的数值是：%d,地址是：%p\n", num, &num)

	num = 200
	fmt.Println("num的数值是：%d,地址是：%p\n", num, &num) //对同一个变量进行重复赋值时，只改变变量的值，不改变地址

	//fmt.Println(num2)  //undefined: num2

	var name string
	//name = 100  //'100' (类型 untyped int) 不能由类型 string 表示
	name = "阿尔法"
	fmt.Println(name)

	//var name string="李晓华"  // 此块中重新声明了 'name'
	//fmt.Println(name)

	//num, name := 1000, "李晓华" //  ':=' 的左侧没有新变量
	//fmt.Println(num, name)

	num, name, sex := 1000, "李晓华", "男"
	fmt.Println(num, name, sex)

	fmt.Println(a)
	fmt.Println(b)

	var m int //整数默认值为0
	fmt.Println(m)
	var n float64 //浮点数默认值0.0->0
	fmt.Println(n)
	var h string //字符串默认值为空的字符串：""
	fmt.Println(h)
	var s []int //切片默认值为空的切片，[]
	fmt.Println(s)

}

```



### 常量初始化

显式类型定义：const  变量名  变量类型  =  值（类型只能为整型，浮点，字符，布尔）

隐式类型定义：const  变量名 = 值

 const (

​		变量名 = 值

​		变量名 = 值

)



常量可以只定义不使用

当只定义常量没赋值时，默认该常量与上一组常量值相同



```go
package main

import "fmt"

func main() {
	//直接输出一些整数（固定的值）
	fmt.Println(100)
	fmt.Println("hello")

	const PATH string = "http:www.baidu.com"
	const PI = 3.14
	fmt.Println(PATH, PI)
	//PATH="http:www.google.com"  //无法分配给 PATH

	const c1, c2, c3 = 100, 3.14, "hi"
	const (
		a int = 100
		b
	)
	fmt.Println("%T,%d\n", a, a)
	fmt.Println("%T,%d\n", b, b)
    
    /*iota关键字 特殊的常量（值可以改变）
	第一行的iota为0，之后每到新的一行iota的值+1
	 */
	
	const (
		a1 = iota   //iota=0=a1
		b1          //iota=1=b1
		l1          //iota=3=l1
		d1 = "haha" //d1=haha,iota=4
		e1          //e1=haha,iota=5 (没定义值时，默认与上一组的值和类型相同)
		f1 = 100    //f1=100,iota=6
		g1          //g1=100,iota=7
		h1 = iota   //iota=8=h1
		k1          //iota=9=k1
	)

}

```



 





