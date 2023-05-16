[@RequestMapping](https://github.com/RequestMapping)注解用于映射url到控制器类或一个特定的处理程序方法。可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。



在springmvc.xml配置文件中添加视图解析器

```java
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <!--配置前缀-->
          <property name="prefix" value="/WEB-INF/templates/"/>
          <!-- 配置后缀-->
          <property name="suffix" value=".jsp"/>
</bean>
```



此注解可以加在方法上，是为当前方法注册一个可以访问的名称（路径）

```java
@RequestMapping("/demo")
public String portal(){
    System.out.println("服务器访问到了。。。。。。。");
    //将逻辑视图返回
    return "main";    //可以访问到/templates/main.jsp页面上
}
```

```html
<a href="<%=request.getContextPath()%>/demo">访问</a>      前端的超链接的样式
```





此注解可以加在类上，相当于包名（虚拟路径），用来区分不同类中相同的请求路径

**例如：在上面的类上添加：@RequestMapping("/user")    相当于访问/templates/user/main.jsp**





此注解可以区分get请求和post请求

```java
@RequestMapping("/req",method=RequestMethod.GET)
public String portal(){
    System.out.println("处理get请求。。。。。。。");
    //将逻辑视图返回
    return "main";    //可以访问到/templates/req/main.jsp页面上
}
```

```java
@RequestMapping("/req",method=RequestMethod.POST)
public String portal(){
    System.out.println("处理post请求。。。。。。。");
    //将逻辑视图返回
    return "main";    //可以访问到/templates/req/main.jsp页面上
}
```







### RestFul风格



**概念**
Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

**功能**
资源：互联网所有的事物都可以被抽象为资源
资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。
分别对应 添加、 删除、修改、查询。



**传统方式操作资源** ：通过不同的参数来实现不同的效果！方法单一，post 和 get

 http://127.0.0.1/item/queryItem.action?id=1 查询,GET
​ http://127.0.0.1/item/saveItem.action 新增,POST
​ http://127.0.0.1/item/updateItem.action 更新,POST
​ http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST



**使用RESTful操作资源** ： 可以通过不同的请求方式来实现不同的效果！如下：请求地址一样，但是功能可以不同！

 http://127.0.0.1/item/1 查询,GET
​ http://127.0.0.1/item 新增,POST
​ http://127.0.0.1/item 更新,PUT
​ http://127.0.0.1/item/1 删除,DELETE



**学习测试**

1. 在新建一个类 RestFulController

   ```java
   @Controllerpublic class RestFulController {}
   ```

2. 在Spring MVC中可以使用 [@PathVariable](https://github.com/PathVariable) 注解，让方法参数的值对应绑定到一个URI模板变量上。

   ```java
   @Controller
   public class RestFulController {
       //映射访问路径
       @RequestMapping("/commit/{p1}/{p2}")
       public String index(@PathVariable int p1, @PathVariable int p2, Model model){
           int result = p1+p2;
           //Spring MVC会自动实例化一个Model对象用于向视图中传值
           model.addAttribute("msg", "结果："+result);
           //返回视图位置
           return "test";
       }
   }
   ```

3. 我们来测试请求查看下

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudyf5b66304-5add-4c41-836d-d0a54aff420a.png)

思考：使用路径变量的好处？

- 使路径变得更加简洁；
- 获得参数更加方便，框架会自动进行类型转换。
- 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，如这里访问是的路径是/commit/1/a，则路径与方法不匹配，而不会是参数转换失败。

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy36c6e329-6d53-4b70-9c3f-cc57fab17e36.png)

1. 我们来修改下对应的参数类型，再次测试

   ```java
   @Controller
   public class RestFulController {
       //映射访问路径
       @RequestMapping("/commit/{p1}/{p2}")
       public String index(@PathVariable int p1, @PathVariable int p2, Model model){
           int result = p1+p2;
           //Spring MVC会自动实例化一个Model对象用于向视图中传值
           model.addAttribute("msg", "结果："+result);
           //返回视图位置
           return "test";
       }
   }
   ```

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy4acae90c-e840-485c-b9ac-55a4482620ff.png)

**使用method属性指定请求类型**

 用于约束请求的类型，可以收窄请求范围。指定请求谓词的类型如GET, POST, HEAD, OPTIONS, PUT, PATCH, DELETE, TRACE等

我们来测试一下：

- 增加一个方法

  ```java
  @Controller
  public class RestFulController {
      //映射访问路径
      @RequestMapping("/commit/{p1}/{p2}")
      public String index(@PathVariable int p1, @PathVariable int p2, Model model){
          int result = p1+p2;
          //Spring MVC会自动实例化一个Model对象用于向视图中传值
          model.addAttribute("msg", "结果："+result);
          //返回视图位置
          return "test";
      }
  }
  ```

- 我们使用浏览器地址栏进行访问默认是Get请求，会报错405：

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudyafe79416-8b3d-487a-8ea4-3aa339bc01dd.png)

- 如果将POST修改为GET则正常了；

  ```java
  @Controller
  public class RestFulController {
      //映射访问路径
      @RequestMapping("/commit/{p1}/{p2}")
      public String index(@PathVariable int p1, @PathVariable int p2, Model model){
          int result = p1+p2;
          //Spring MVC会自动实例化一个Model对象用于向视图中传值
          model.addAttribute("msg", "结果："+result);
          //返回视图位置
          return "test";
      }
  }
  ```

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy9fba8ea7-f80c-4f1a-926b-c5d99574cce0.png)

**小结：**

Spring MVC 的 [@RequestMapping](https://github.com/RequestMapping) 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PATCH。

**所有的地址栏请求默认都会是 HTTP GET 类型的。**

方法级别的注解变体有如下几个： 组合注解

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

[@GetMapping](https://github.com/GetMapping) 是一个组合注解

它所扮演的是 [@RequestMapping](https://github.com/RequestMapping)(method =RequestMethod.GET) 的一个快捷方式。

平时使用的会比较多！
