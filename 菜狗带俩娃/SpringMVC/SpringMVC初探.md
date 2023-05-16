### 什么是SpringMVC：

* 是基于MVC开发模式的框架，用来优化控制器，是Spring家族的一员，他也具备IoC和AOP
* 与Servlet技术功能相同，属于web层开发技术

什么是MVC：是一种开发模式，所有的web应用都是基于MVC开发



当发起请求时被前置的控制器拦截到请求，根据请求参数生成代理请求，找到请求对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应给中心控制器，控制器使用模型与视图渲染视图结果，将结果返回给中心控制器，再将结果返回给请求者

![image-20230325122037342](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230325122037342.png)

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy00854e07-7eac-476c-a9dd-dcebb7ac0b89.png)

### SpringMVC优点：

* 轻量级，基于MVC框架
* 易上手，容易理解，功能强大
* 具备IoC和AOP
* 完全基于注解开发





### SpringMVC执行流程

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy0214fd0a-0df0-4910-a467-5b7d61712868.png)

**简要分析执行流程**

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。

我们假设请求的url为 : http://localhost:8080/SpringMVC/hello

**如上url拆分成三部分：**

[http://localhost:8080服务器域名](http://localhost:8080服务器域名/)

SpringMVC部署在服务器上的web站点

hello表示控制器

通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。

1. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。
2. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。
3. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。
4. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。
5. Handler让具体的Controller执行。
6. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。
7. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。
8. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。
9. 视图解析器将解析的逻辑视图名传给DispatcherServlet。
10. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。
11. 最终视图呈现给用户。





### 开发步骤

![image-20230409154952600](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230409154952600.png)

* 新建项目，选择webapp模板
* 对pom.xml文件进行修改，添加servlet、springmvc的依赖
* 添加springmvc.xml文件，指定包扫描，添加视图解析器
* 将web.xml文件的初始内容更改为如下所示，并在其中注册springmvc框架

```xml
<servlet>
  <servlet-name>SpringMVC</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:SpringMVC-servlet.xml</param-value>
  </init-param>
</servlet>
<servlet-mapping>
  <servlet-name>SpringMVC</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>

/：处理所有文件，不包含.jsp文件
/*：处理所有文件，包含.jsp文件
```

* 在webapp下新建admin目录（这里的目录名要与视图解析器中的相对应），在该目录下创建main.jsp

* 开发控制器（Servlet），他是一个普通的类

* 

* ```java
  @RequestMapping("/demo")     
  public String portal(){
      System.out.println("服务器访问到了。。。。。。。");
      //将逻辑视图返回
      return "main";   //返回值是该文件的文件名（main.jsp）
  }
  ```

* 添加tomcat进行测试



**Controller中方法的返回值：**

* **String**：客户端资源的地址，自动拼接前缀和后缀，还可以屏蔽自动拼接字符串，可以指定返回的路径
* **Object**：返回json格式的对象，自动将对象或集合转为json，使用工具jackson工具进行转换，必须要添加jackson依赖，一般用于ajax请求
* **void**：无返回值，一般用于ajax请求
* **基本数据类型**：用于ajax请求
* ModeAndView：返回数据和视图对象，用的很少



web请求执行流程：

main.jsp <-----------------------------> DispatcherServlet（核心处理器）<-----------------------------> SpringMVC的处理器是一个普通的方法



web.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
</web-app>
```





![image-20230411181535466](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230411181535466.png)



