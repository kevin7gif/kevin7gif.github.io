### **map**



map：映射，一种专门用于存储键值对的集合，属于**引用**类型

存储特点：存储的是**无序**的键值对；键不能重复，并且要和value值一一对应，如果map中的key重复了，那么新的value值会覆盖原来的，程序不会报错

语法

> 创建map
>
> var  map1  map[ key类型 ] value类型     （此时map是nil的，无法直接使用）
>
> var  map2 = make(map[ key类型 ] value类型 )      使用make函数创建map
>
> var  map3 = map[ key类型 ]value类型{key:value ,key:value ,key:value...}



```go
//创建map
var map1 map[int]string
var map2 = make(map[int]string)
var map3 = map[string]int{"go":33,"jaba":56}

//判断map是否为nil
fmt.Println(map1 == nil)
fmt.Println(map2 == nil)
fmt.Println(map3 == nil)

//nil map
if map1 == nil{
    map1 = make(map[int]string)
    fmt.Println(map1 == nil)
}


//存储键值对到map中,map1[key] = value
map[1] = "hello"
map[2] = "go"
map[3] = "pt"
map[4] = "ruby"
map[5] = ""


//获取数据，根据key获取对应的value值，如果key存在，获取数据，如果key不存在，获取的是value值类型的零值
fmt.Println(map1)
fmt.Println(map1[4])   //根据key为4，获取对应的value值：ruby
fmt.Println(map1[20])   //不存在一个20的key，所以获取零值"


//  value,ok := map[key]  判断当前key是否存在，若存在返回true，不存在返回false
value,ok := map[20]
if ok{
    fmt.Println("对应的数值是：",value)
}else{
    fmt.Println("操作的key不存在，获取到的是零值：",value)
}


//修改数据,如果修改的key并不存在，则就是在该key值上存储数据
map1[3] = "python"
fmt.Println(map1[3]) 

//删除数据，用map的内置函数delete()删除，如果key存在则直接删除，如果key不存在，对map也没有影响
delete(map1,3)   //第一个参数为map的名字，第二个参数为要删除的键值对的key

//长度
len(map1)
```



### 每种数据类型的零值

int：0

float：0.0——>0

string：""    空的字符串

array：[对应元素数据类型的零值]

silce：nil（类似于null） 空的切片是有底层数组的，故可以直接使用

map：nil（类似于null）空的map无法直接使用



### **map的遍历**

使用：for  range

>数组、切片：index，value
>
>map：key，value

```go
//创建map
map1 := make(map[int]string)
map1[1] = "红孩儿"
map1[2] = "小旋风"
map1[3] = "白骨精"
map1[4] = "马化腾"

//遍历map（无序）
for key,value := range map1{
    fmt.Println(key,value)    //输出结果并不一定与创建map的顺序一致，因为map是无序的
}

//遍历map（有序）
for i:=1;i<=len(map1);i++{
    fmt.Println(map1[i])
}//如果key值不连续，则输出会不太好看

/*有序并且连续的遍历map
获取所有的key，————>切片/数组
进行排序
遍历key，————>map[key]
*/
keys := make([]int,0,len(map1))

for k,_:=range map1{
    keys=append(keys,k)
}

//可以自己写排序算法，也可以直接使用sort包下的排序方法
sort.Ints(keys)

for _,key := range keys{
    fmt.Println(key,map1[key])
}

```





### **map与slice结合**

>创建map用于存储人的信息name，age，sex，address
>
>每个map存储一个人的信息
>
>将这些map存入到slice中
>
>打印遍历输出

```go
//1.创建map存储第一个人的信息
map1 := make(map[string]string)
map1["name"]="太白金星"
map1["sex"]="男"
map1["age"]="16"
map1["address"]="北京"

//2.创建map存储第二个人的信息
map2 := make(map[string]string)
map2["name"]="太上老君"
map2["sex"]="男"
map2["age"]="61"
map2["address"]="上海"

//将map存入到slice中
s1 := make([]map[string]string,0,3)
s1 = append(s1,map1)
s1 = append(s1,map2)

//遍历切片
for i,val :=range s1{   //val：map1，map2，map3
    fmt.Printf("第%d个人的信息是：\n",i+1) //切片下标从0开始
    fmt.Printf("\t姓名：%s\n",val["name"])
    fmt.Printf("\t性别：%s\n",val["sex"])
    fmt.Printf("\t年龄：%s\n",val["age"])
    fmt.Printf("\t地址：%s\n",val["address"])
}


```



**map是引用类型**：多个变量共享一块内存

