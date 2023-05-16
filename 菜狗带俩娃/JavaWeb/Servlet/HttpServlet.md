### HttpServlet源码分析

* HttpServlet类是专门为HTTP协议准备的，比GenericServlet更适合HTTP协议下的开发
* HttpServletRequest简称request对象，其中封装了请求协议的全部内容
* Tomcat服务器（WEB服务器）将“请求协议”中的数据全部解析出来，然后再把这些数据全部封装到request对象中
* HttpServletResponse对象专门用来响应HTTP协议到浏览器

---

## 最终的Servlet类开发步骤

* 第一步：编写一个Servlet类，直接继承HttpServlet类
* 第二步：重写doGet方法或doPost方法，至于重写谁，Java程序说了算
* 第三步：将Servlet类配置到web.xml文件
* 第四步：准备前端界面（form表单），form表单中指定路径即可



---

### WEB站点的欢迎页面

* 对于一个webapp来说，设置了欢迎页面之后，当你访问这个webapp，或者范围跟这个站点的时候，没有指定任何“资源路径”，这时会默认访问欢迎页面
* 一般的访问方式是：
  * http://localhost:8080/servlet1/test.html，这种方式指定了要访问的就是test.html这个资源

* 如果访问方式是：
  * http://localhost:8080/servlet1，没有指定具体的资源路径，默认访问欢迎页面



**设置欢迎页面**

* 第一步：在idea的web目录下新建了一个文件login.html

* 第二步：在web.xml中进行以下配置

  * ```xml
     <welcome-file-list>
            <welcome-file>login.html</welcome-file>
     </welcome-file-list>
    ```

  * 设置欢迎页面时，这个路径不需要以“/”开始，并且这个路径默认从webapp的根下开始查找

* 第三步：启动服务器，浏览器地址栏输入：

  * http://localhost:8080/Servlet4 ------>(Servlet4为我自己新建的模块)

* 如果在webapp的根下新建一个目录，目录中再给一个文件，要这么设置：

  * 在webapp根下新建page1

  * 在page1下新建page2

  * 在page2下新建index.html文件

  * 在web.xml文件中要进行如下配置

  * ```xml
    <welcome-file-list>
            <welcome-file>page1/page2/page.html</welcome-file>
    </welcome-file-list>
    ```

* 一个webapp可以设置多个欢迎页，代码写的越靠上的优先级越高

  ```xml
  <welcome-file-list>
      	<welcome-file>login.html</welcome-file>
          <welcome-file>page1/page2/page.html</welcome-file>
  </welcome-file-list>
  ```

* 当有文件名为index.html的文件时，不需要在web.xml文件中进行配置欢迎页面，打开地址栏输入url会直接进入index.html这个文件，这是因为Tomcat服务器已经提前配置好了

* 配置欢迎页面有两个地方可以配置：

  * 一个是在webapp内部的web.xml文件中（局部配置）

  * 一个是在CATALINA_HOME/conf/web.xml文件中进行配置（全局配置）

  * ```xml
    <welcome-file-list>
        	<welcome-file>index.html</welcome-file>
            <welcome-file>index.htm</welcome-file>
        	<welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
    ```

    * Tomcat服务器的全局欢迎页面是：index.html，如果一个web站点没有设置局部的欢迎页面，Tomcat服务器就会以index.html作为一个web站点的欢迎页面

  * **局部优先原则**

---

### 关于WEB-INF目录

* 在WEB-INF目录下新建一个文件：welcome.html
* 打开浏览器访问：http://localhost:8080/Servlet4/WEB-INF/welcome.html会出现404错误
* 放在WEB-INF目录下的资源都是受保护的，在浏览器上不能通过路径直接访问，所以像HTML，css，js，image等静态资源都要放在WEB-INF目录之外

---

### HttpServletRequest接口详解

* HttpServletRequest接口是Servlet规范中的一个
* 他的父接口是ServletRequest
* Tomcat服务器实现了HttpServletRequest接口
* HttpServletRequest对象是由Tomcat服务器创建的，这个对象中封装了HTTP的请求协议
* 实际上是用户发送请求的时候，遵循了HTTP协议，发送的是HTTP请求协议，Tomcat服务器将HTTP协议中的信息以及数据全部解析出来，然后Tomcat服务器把这些信息封装到HttpServletRequest对象当中，传给Java程序
* request和response对象的生命周期：
  * 一个是请求对象一个是响应对象，这两个请求只在当前请求中有效
  * 一次请求对应一个request
  * 两次请求对应两个request

* HttpServletRequest接口的常用方法：

  * 怎么获取前端浏览器用户提交的数据

    ```java
    Map<String ,String[]> getParameterMap() 这个是获取Map
    Enumeration <String> getParameterNames() 这个是获取Map集合中所有的key
    String[] getParameterValues(String name) 根据key获取Map集合的value
    String getParameter(String name) 获取value这个一维数组中的第一个元素，这个方法最常用
    //以上方法都和获取用户提交的数据有关系
    ```

    * 前端提交的数据格式：uesrname=...&userpwd=...&aihao=...&aihao=...

    * 采用Map集合来存储数据最合适

    * ```java
      Map<String String[]>
      如果map中的key和value都采用String的话，如果key有多个值的话，value会覆盖
      key               value
      --------------------------
      username           ["abc"]
      userpwd			  ["111"]
      aihao			  {"s","d","tt"}
      ```

    * 注意：前端表单假设提交了120这个数据，其实是以字符串"120"提交的，在后端获取到的是字符串"120"（前端永远提交的是字符串，后端获取的也永远是字符串）

![image-20230222232701739](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230222232701739.png)



![image-20230222232956917](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230222232956917.png)



![image-20230222233200292](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230222233200292.png)



---

* request对象实际上又称为”**请求域对象**“
  * ServletContext（Servlet上下文对象）是**应用域对象**
    * 当所有用户共享的数据，数据量很小，这个共享的数据很少进行修改，使用应用域对象。可提高程序执行效率
  * 向应用域中绑定数据，相当于把数据放到缓存中，当用户访问的时候直接从缓存中取，减少IO操作，提升系统性能。缓存技术是提高系统性能的重要手段

* 已经学过的缓存技术
  * 字符串常量池
  * 整数常量池
  * 数据库连接池（提前创建好N个连接对象，将连接对象放到集合当中，直接从缓存中拿，省去了连接对象的创建过程）
  * 线程池（Tomcat服务器是支持多线程的，线程池就是提前创建好N个线程对象，将线程对象存储到集合中，然后用户请求过来后，直接从线程池中获取线程对象）
  * redis等

* ServletContext中有三个操作应用域的方法

* ```java
  void setAttribute(String name,Object obj)//向域中绑定数据
  Object getAttribute(String name)//从域中根据name获取数据
  void removeAttribute(String name)//将域当中绑定的数据移除
      
  //以上操作类似于Map集合的操作
  Map<String,Object>map
  map.put("name",obj)//向map集合中放key和value
  Object obj=map.get("name")//通过map集合的key获取value
  map.remove("name")//通过map集合的key删除key和value这个键值对
  ```

* 

* **请求域对象**

  * 请求域对象比应用域对象范围小很多，生命周期短很多。请求域只在一次请求内有效

  * 一个请求对象对应一个请求域对象，一次请求结束这个请求域就销毁了

  * 请求域对象也有这三个方法：

  * ```java
    void setAttribute(String name,Object obj)//向域中绑定数据
    Object getAttribute(String name)//从域中根据name获取数据
    void removeAttribute(String name)//将域当中绑定的数据移除
    ```

  * 请求域和应用域的选用原则：

    * 尽量使用小的域对象，因为小的域对象占用资源少

  

**请求转发器**：可以让一个类执行之后跳转到另一个类执行

* 第一步：获取请求转发器

* ```java
  RequestDispatcher dispatcher=request.getRequestDispatcher("/test2");
  //其中方法内的参数是要下一个要跳转的类的资源路径
  ```

* 第二步：调用请求转发器的forward方法完成跳转

* ```java
  dispatcher.forward(request,response);
  ```

  

两个Servlet类怎么共享数据？

* 将数据放到ServletContext应用域中是可以的，但是应用域范围太大占用资源太多，不建议使用
* 可以把数据放到request域中，然后由一个Servlet类转发到另一个Servlet类，保证两个Servlet类在同一次请求当中，这样就能做到两个或多个Servlet类共享同一份数据
* 转发到下一个资源不一定是Servlet类，只要是TOomcat服务器内的合法资源都行，如：html
* 转发的时候，路径的写法是web.xml文件里的资源路径，不用加项目名



容易混淆的两个方法

```java
String uesrname=request.getParameter("username");
Object obj=request.getAttribute("name");
//第一个方法：获取的是用户在浏览器上提交的数据
//第二个方法：获取的是请求域中绑定的数据
```



HttpServletRequest接口的其他常用方法

```java
//获取客户端地址
String remoteAddr=request.getRemoteAddr();

//设置请求体的字符集，这个方法是处理post请求的乱码问题，并不能解决get方法的乱码问题
//因为get请求实在请求行上提交数据，post请求是在请求体中提交数据
//Tomcat10之后，request请求体中的字符集默认是UTF-8,不会出现乱码问题
request.setCharacterEncoding("UTF-8");

//从Tomcat8之后，get请求就没有乱码问题了

//获取应用的根路径
String contextPath=request.getContextPath()
    
//获取请求方式
String method=request.getMethod()
    
//获取请求的URI
String uri=request.getRequestURI()
    
//获取servlet path
String servletpath=request.getServletPath()
```

