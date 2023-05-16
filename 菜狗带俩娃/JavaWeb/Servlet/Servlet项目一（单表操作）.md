## 使用纯Servlet做一个单表的CRUD（增删改查）操作

* 对部门的增删改查操作（B/S结构）

* 实现步骤

  * 第一步：准备一张数据库表

    ```mysql
    create table dept(
    	deptno int primary key,
    	dname varchar(255),
    	loc varchar(255),
    );
    insert into dept values(10,'销售部','北京');
    insert into dept values(20,'研发部','上海');
    insert into dept values(30,'技术部','广州');
    insert into dept values(40,'媒体部','深圳');
    select * from dept;
    ```

  * 第二步：准备一个HTML页面（项目原型）

    * 把HTML页面准备好，然后将HTML页面中的链接都能跑通
    * 要设计的页面：
      * 新增页面：add.html
      * 修改页面：edit.html
      * 查看详情页面：detail.html
      * 欢迎页面：index.html
      * 部门列表页面：list.html（以列表页面为核心进行操作）

  * 第三步：分析系统有哪些功能

    * 查看部门列表
    * 新增用户
    * 删除用户
    * 查看部门详细信息
    * 跳转到修改界面修改部门信息

  * 第四步：在idea中搭建开发环境

    * 创建一个webapp
    * 向webapp中添加连接数据库的jar包（mysql驱动）
    * JDBC的工具类

  * 第五步：实现第一个功能：查看部门列表
  
    * 第一步：先修改前端页面的超链接
    * 第二步：编写web.xml文件
    * 第三步：编写deptList类继承HttpServlet类，然后重写doGet方法
    * 第四步：在deptList类的doGet方法中连接数据库，查询所有部门，动态展示部门列表信息
      * 分析list.html页面中哪部分是固定的，哪部分是需要动态展示的
      * 只用servlet写代码太过繁琐
  
  * 第六步：实现查看部门详情功能
  
    * 从前端往后端一步一步实现。用户点击的是哪里
  
    * 用户点击的“详情按钮”在Java程序中，这个详情是需要链接数据库的，所以这个超链接点击之后也是需要执行一段Java代码，所以要将这个超链接的路径进行修改
  
    * 技巧：写根路径的时候不用吧根路径写死，调用getContextPath方法即可
  
      * ```java
                       out.print("            <a href='"+contextPath+"/detail?deptno="+deptno+"'>详细信息</a>");
        ```
  
      * 向服务器提交数据的格式：uri?name=value&name=value
  
    * 编写一个deptDetail类继承HttpServlet类，然后重写doGet方法
  
    * 在doGet方法中，连接数据库根据部门编号查询部门信息，动态展示部门详情页
  
  * 第七步：删除用户
  
    * 用户点击删除的时候，应该提示用户是否删除
    * 将前端程序写到后端代码的doGet方法当中
    * 编写deptDelete类继承HttpServlet，重写doGet方法
    * 删除成功或删除失败时的处理
  
  * 第八步：新增部门
  
    * 如果保存使用的是post请求。当转发到/list的时候会出现405错误
    * 因为转发是一次请求，之前是post之后还是post
    * 保存用的是post请求，底层要执行doPost方法
    * /list中只有一个doGet方法
    * 解决方案：
    * 第一种：在/list类中添加doPost方法，然后在doPost方法中调用doGet方法
    * 第二种：重定向
  
  * 第九步：跳转到修改用户信息的界面
  
  * 第十步：修改用户信息



---

### 在一个web应用这如何完成资源的跳转

* 第一种方式：转发

* 第二种方式：重定向

* 二者的区别

  * 代码上的区别

    * 转发

      * ```java
        RequestDispatcher dispatcher=request.getRequestDispatcher("请求路径，不带项目名")
        disatcher.forWard(request,response)
        //转发的时候是一次请求，不管转发多少次都在同一个request当中，这是因为在调用forward方法时，会将当前的request和response对象传递给下一个servlet
        ```

    * 重定向

      * ```java
        //以下代码会将请求路径发送给浏览器，浏览器会自发的向服务器发送一次全新的请求
        
        //浏览器发送请求，请求路径上需要加项目名
        response.sendRediect("请求路径")
        ```
  
      * 
  
  * 形式上的区别：
  
    * 转发（一次请求）
  
      * 在浏览器地址栏上发送的请求路径是什么，请求结束后，浏览器地址栏上的路径还是这个

    * 重定向（两次请求）

      * 在地址栏上发送的请求是a，最终在浏览器地址栏上显示的地址是b
  
        
  
        转发和重定向的本质区别：
      
      * 转发：由WEB服务器来控制，A资源跳转到B资源，这个跳转动作是Tomcat服务器内部完成的
      * 重定向：是浏览器完成的，具体跳转到哪个资源，服务器说了算
  
      
      

**转发：**

![image-20230226102023415](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230226102023415.png)



**重定向**：

![image-20230226102903305](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230226102903305.png)



转发和重定向如何选择？

* 如果在上一个Servlet中向request域中绑定了数据，希望从下一个Servlet中把request域里面的数据取出来，使用转发机制
* 剩下的所有请求均使用重定向（重定向使用较多）.
* 跳转的资源只要是服务器内部合法的资源即可，包括：Servlet，JSP、HTML

---

### 将oa项目中的资源跳转修改为合适的跳转方式

* 删除之后，重定向
* 修改之后，重定向
* 保存之后，重定向
* 重定向：
  * 成功
  * 失败



### Servelt注解简化配置

* 分析oa项目中的web.xm文件

  * 现在只是一个简单的CRUD，没有复杂的业务逻辑。但是web.xm文件中就有这么多的配置信息，如果采用这种方式，对于一个大项目来说该文件会很庞大
  * 在web.xml文件中进行servlet信息的配置，开发效率很低，每个都需要配置一下
  * 而且xml文件中的配置很少修改，这种配置信息是可以直接写到java类中的

* Servlet3.0之后，推出了各种Servlet基于注解式开发

  * 开发效率高，不需要编写大量的配置信息
  * web.xml文件体积变小

* 但是即使有注解，xml文件还是需要的

  * 一些需要变化的信息还是要配置到xml文件中，一般都是注解+配置文件的开发模式
  * 一些不会经常变化修改的配置建议使用注解。

* 第一个注解：

  * ```java 
    import jakarta.servlet.annotation.WebServlet;
    ```

  * 在Servlet类上使用：@WebServlet，该注解的属性：

    * name属性：用来指定Servlet的名字，等同于：<servlet-name>
    * urlPatterns属性：用来指定Servlet的映射路径，可以指定多个字符串，等同于：<url-pattern>
    * loadOnStartup属性：用来指定在服务器启动阶段是否加载该Servet，等同于：<load-on-startup>
    * value属性：和urlPatterns属性一样，都是用来指定Servlet的映射路径的，value可省略不写例如：@WebServlet(value="/welcome")------>@WebServlet("/welcome")
    * 属性是一个数组，如果数组中只用一个元素，数组的大括号可以省略

* 注解对象的使用格式

  * @注解名称(属性名=属性值，属性名=属性值.....)



### 使用模板方法设计模式优化oa项目

* 上面的注解解决了配置文件的问题，但是现在的oa项目仍然存在一个比较臃肿的问题
  * 一个单表的CRUD，就写了这么多Servlet类。如果一个复杂的业务系统，这种开发方式显然会存在类爆炸（类的数量太大）
  * 可以使用模板方法设计模式解决类爆炸问题
* 怎么解决类爆炸问题？
  * 以前的设计是一个请求对应一个Servlet类，导致类爆炸
  * 可以让一个请求对应一个方法，一个业务对应一个Servlet类
  * 一个部门处理业务对应一个DeptServlet类，一个用户处理业务对应一个UserServlet类



### 分析使用纯粹Servlet开发Web应用的缺陷

* 在Servlet当中编写HTML/CSS/JavaScript等前端代码，存在什么问题？
  * Java中编写前端代码，难度大，麻烦
  * Java中编写前端代码，程序的耦合度太高
  * Java中编写前端代码，代码非常不美观，维护成本高
    * 修改小小的一个前端代码，只要有改动，就需要重新编译java代码，生成新的class文件，打一个新的war包，重新发布

* 如何解决这个问题？
  * 上面的那个Servlet程序能不能不写，让机器自动生成。程序员只需要写这个Servlet程序中的”前端的那部分代码“，然后让机器将我们写的”前端代码“自动翻译成Servlet程序，然后机器再自动将Java程序编译生成class文件，然后在使用JVM调用这个class中的方法



## 关于B/S结构系统的会话机制（session机制）

* 用户打开浏览器，在浏览器上进行的一系列操作之后,最终将浏览器关闭，这整个过程叫做：一次会话。会话在服务器也有一个对应的Java对象：session
* 用户在浏览器上点击了一下，然后到页面停下来，可以粗略的认为是一次请求，请求对应的服务器也有一个Java对象：request
* 一个会话中包含多次请求（一次会话对应N次请求）
* 在Java的servlet规范中，session对应的类名：jaakata.servlet.http.HttpSession
* session机制是B/S结构的一部分，如果使用PHP开发web项目，同样也是有session这种机制的。session机制实际上是一种规范，不同的语言对这种会话机制都有实现
* session对象最主要的作用：保存会话状态（比如：用户登陆成功后，怎么把登陆状态一直保存起来？就是用session对象来保存这种会话状态的）
* #### 为什么需要session对象来保存会话状态呢？
  
  * 因为HTTP是一种无状态协议
  * 无状态：请求的时候，B和S是连接的，但是请求结束之后，链接就断了。这种无状态协议可以降低服务器的压力，请求的瞬间是链接的，请求结束之后，链接断开。
  * 只要B和S断开了，那么关闭浏览器这个动作，服务器是不知道的
  
* #### 为什么不使用request对象保存会话状态？为什么不使用ServletContext对象保存会话状态？
  
  * request.setAttribute()存，request.getAttribute()取，ServetContext也有这个方法。request是请求域，ServletContext是应用域
  * request是一次请求对象
  * ServletContext对象是服务器启动的时候创建，服务器关闭的时候销毁，ServletContext对象只有一个
  * ServletContext对象域太大
  * request请求域(HttpServletRequest)、session会话域(HttpSession)、application域(ServletContext)
  * request<sessioon<application
* #### session对象的实现原理：
  
  * HttpSession session=request.getSession();
  * 这行代码里：张三访问的时候获取到session对象就是张三的，李四访问的时候获取到session对象就是李四的
* ![image-20230303000217951](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230303000217951.png)



![image-20230303002540326](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230303002540326.png)



浏览器关闭的时候，服务器是不知道的，所以session的销毁要依靠session超时机制

也有一种可能，系统提供了安全退出，用户点击这个按钮，这样浏览器就知道用户退出了，服务器会自动销毁这个session对象

* session的实现原理：
  * JSESSIONID=xxxxxxxx，这个是以cookie的形式保存在浏览器的内存中，浏览器只要关闭，这个cookie就没有了
  * session列表是一个Map，map的key是sessionid，value是session对象
  * 用户第一次请求的时候：服务器生成session对象同时生成id，将id发送给浏览器
  * 用户第二次请求的时候：自动将浏览器内存中的id发送给服务器，服务器根据id查找session对象
  * 关闭浏览器，内存消失，cookie消失，sessionid消失，会话等同于结束



* #### Cookie禁用了，session还能找到吗？
  
  * cookie禁用：服务器正穿发送cookie给浏览器，但是浏览器不要了，拒收。
  * 如果禁用了cookie，那么用户在进行请求时，每一次服务器都会生成一个新的session对象
  * cookie禁用之后，还是可以实现session机制的
    * 需要使用URL重写机制
      * 例如：http://localhost:8080/oa/session;jsessionid=19D1C99560DCBF84839FA43D58F56E16
      * URL重写机制会提高开发者的成本，开发人员在编写任何请求路径的时候，后面都要添加一个sessionid，给开发带来很大的难度、成本。



* 目前为止所了解的域对象：
  * request（请求域，请求级别的）
    * 对应的类名：HttpServletRequest
  * session（会话域，用户级别的）
    * 对应的类名：HttpSession
  * application（应用域，项目级别，所以用户共享）
    * 对应的类名：ServletContext
  * 三个域的大小关系：
    * request<session<application
  * 三个域都有以下三个方法：
    * setAttribute（向域中绑定数据）
    * getAttribute（从域中获取数据）
    * removeAttribute（删除域当中的数据）
  * 使用原则：尽量使用小的域



* #### session掌握之后，如何解决oa项目的登陆问题，怎么让登录起作用？

  * 登陆成功之后，可以将用户的登录信息存储到session当中，也就是说session中如果有用户的信息就代表用户登陆成功了，session中如果没有用户信息表示用户没有登陆过，则跳转到登录页面
  
  * 销毁session对象
  
    * ```java
      session.invalidate();
      ```



## Cookie

* session的实现原理中，每一个session对象都会关联一个sessionid，例如：

  * JSESSIONID=19D1C99560DCBF84839FA
  * 以上的这个键值对数据其实就是cookie对象
  * 对于session关联的cookie来说，这个cookie是被保存到浏览器的“运行内存”当中
  * 只要浏览器不关闭，用户再次发送请求的时候，会自动将运行内存中的cookie发送给服务器
  * 例如：这个Cookie：JSESSIONID=19D1C99560DCBF84839FA就会再次发送给服务器
  * 服务器就是根据19D1C99560DCBF84839FA这个值来找到对应的session对象的

* cookie如何生成？cookie保存在什么地方？cookie有什么用？浏览器什么时候发送cookie？发送哪些cookie给服务器？

  * cookie最终保存在浏览器客户端上。

    * 可以保存在运行内存中（浏览器只要关闭cookie就消失）

    * 也可以保存在硬盘文件（永久保存）

  * cookie作用：

    * cookie和session机制都是为了保存会话状态
    
    * cookie是将会话的状态保存在浏览器客户端上（cookie数据存储在浏览器客户端上）
    
    * session是将会话的状态保存在服务器上（session对象 存储在服务器上）
    
    * 之所以要有cookie和session机制是因为HTTP协议是无状态 无连接协议
    
      

  

* cookie和session机制都不属于Java中的机制，实际上cookie机制和session机制都是HTTP协议的一部分，PHP开发中也有session和cookie机制，只要是web开发，不管什么语言，cookie和session机制都是需要的

* HTTP协议规定：任何一个cookie都是由name和value组成的，name和value都是字符串类型的

* 在Java的servlet中，对cookie提供了那些支持？

  * 提供了一个Cookie类，专门用来表示cookie数据：jakarta.servlet.http.Cookie
  * java程序怎么把cookie数据发送给服务器？response.addCookie(cookie)

* HTTP协议中是这样规定的：当浏览器发送请求的时候，会自动携带该path下的cookie数据给服务器（URL）



### 关于cookie的有效时间：

* 怎么用Java设置cookie的有效时间
  * cookie.setMaxAge(60*60)：设置cookie在一小时之后失灵
* 没有设置有效时间：默认保存在浏览器的运行内存中，浏览器关闭则cookie消失
* 只要设置cookie的有效时间＞0，这个cookie一定会存储到硬盘文件中
* 设置cookie的有效时间=0，同名cookie被删除
* 设置cookie的有效时间＜0，和不设置一个效果



#### 关于cookie的path，cookie的关联路径是：

* 假设现在发送的请求路径是：http://localhost:8080/oa1/cookie/generate，生成的cookie，如果cookie没有设置path，那么默认的path是：
  * http://localhost:8080/oa1/cookie以及他的子路径
  * 也就是说，只要浏览器的请求路径是：http://localhost:8080/oa1/cookie以及他的子路径，cookie都会被发送到服务器
* 手动设置cookie的path
  * cokie.setPath("/oa1")，表示只要是这个oa1项目的请求路径，都会提交这个cookie给服务器

* 浏览器发送了cookie给服务器，服务器的Java程序如何接受？

  * ```java
    	   Cookie[] cookies = request.getCookies();
            if (cookies != null) {
                //遍历数组
                for (Cookie cookie : cookies) {
                    //获取cookie的name和value
                    String name = cookie.getName();
                    String value = cookie.getValue();
                    System.out.println(name+"="+value);
                }
    ```

  

  ### 使用cookie实现10天内免登录功能

  * 先实现登录功能

    * 登陆成功
      * 跳转到部门列表页面
    * 登录失败
      * 跳转到登陆失败页面

  * 修改前端页面

    * 在登陆页面给一个复选框，复选框后面给一句话：十天内免登录
    * 用户选择了复选框，表示要支持十天内免登录
    * 用户没有选择复选框，表示用户不想使用十天内免登录功能

  * 修改Servlet中的login方法

    * 如果用户登陆成功，并且用户登陆时选择了十天内免登录这个功能，这个时候应该在Servlet的login方法中创建cookie，用来存储用户名和密码，并且设置路径，设置有效期，将cookie响应给浏览器（浏览器将其自动保存在硬盘文件中十天）

  * 用户打开网站到欢迎页的时候，有两个走向：

    * 要么跳转到部门列表页面
    
    * 要么跳转到登陆页面
    
      
    

---

## JSP

* 第一个jsp程序：
  * 在WEB-INF目录之外创建一个index.jsp，然后这个文件中没有任何内容
* 将上面的项目部署之后，启动服务器，打开浏览器，访问以下地址：
  * http://locahost:8080/jsp/index.jsp展现在大家面前的是一个空白页
  * 实际上访问以上的这个：index.jsp，底层执行的是：index_jsp.class这个Java程序
  * 这个index.jsp会被Tomcat翻译生成index_jsp.java文件，然后Tomcat服务器又会把index_jsp.java编译生成index_jsp.class文件
  * 访问index.jsp实际上执行的是index_jsp.class中的方法

* JSP实际上就是一个Servlet
  * Index.jsp访问的时候，会自动翻译生成index_jsp.java，会自动编译生成index_js.class，那么index_jsp就是一个类
  * index_jsp类继承HttpJspBase,而HttpJspBase类继承的是HttpServlet，所以index_jsp就是一个Servlet类
  * jsp的生命周期和Servlet的生命周期完全相同3
  * jsp和Servlet一样，都是单例的（假单例）

* jsp文件第一次访问的时候是比较慢的
  * 第一次比较麻烦：
    * 要把jsp文件翻译生成java源文件
    * java源文件要编译生成class字节码文件
    * 通过class去创建servlet对象
    * 然后调用servet对象的init方法
    * 最后调用servlet对象的service方法
  * 第二次就比较快了
    * 第二次直接调用单例servlet对象的service方法即可

* JSP是什么？
  * JSP是Java程序（JSP本质还是一个Servlet）
  * JSP是JavaServlet Pages的缩写（基于java语言实现的服务器端的页面）
  * Servlet是JavaEE的13个子规范之一，那么JSP也是JavaEE的13个子规范之一
  * JSP是一套规范，所以的web容器/web服务器都是遵循这套规范的，都是按照这套规范进行的翻译
  * 每一个web容器/web服务器都会内置一个JSP翻译引擎

* 对JSP进行错误调试的时候，还是要直接打开JSP文件对应的Java文件，检查Java代码

* **JSP的作用：展示数据**
* **Servlet作用：获得数据**



### JSP的基础语法

* 在jsp文件中直接编写文字，都会被自动翻译到servlet类的service方法的out.writer("翻译到这里")，直接翻译到双引号里，被Java程序当作普通字符串打印输出到浏览器

* 在jsp中编写的HTML CSS  JS代码对于jsp来说只是一个普通的字符串，但是jsp把这些普通的字符串一旦输出到浏览器，浏览器就会对HTML  CSS  JS进行执行解释，展现一个效果

* JSP的page指令，解决响应时的中文乱码问题

  * ```java
    <%@page contextTye="text/html;charset=UTF-8"%>
    ```

* 怎么在JSP中编写Java程序

  * **<% Java语句%>**
    * 在这个符号当中编写的被视为Java程序，被翻译到Servlet类的service方法内部
    * 在<%%>里面写Java代码的时候，要记住正在”service方法体“中写代码，不能写静态代码块，不能写方法，不能定义成员变量
    * 在service方法体中编写的代码是有顺序的，自上而下依次逐行进行
    * 在同一个JSP文件中，<%%>代码块可以出现多个
  * **<%! %>**
    * 在该代码块中写的Java语句会自动被放到service方法体外
    * 这个语法很少使用，因为在service方法外面写静态变量和实例变量，都会存在线程安全问题，JSP就是servlet，是单例的，多线程并发的环境下，这个静态变量和实例变量一旦有所修改必然存在线程安全问题
  * JSP的输出语句
    * <% String name="jack"; out.print(name); %>
    * 以上代码中的out是JSP的九大内置对象之一，可以直接拿来使用，但是必须在service方法内部使用
    * 如果向浏览器上输出的内容没有”Java“代码，例如输出的字符串是一个固定的字符串，可以直接在jsp中编写，不用写到<% %>这里
    * 如果输出端内容中含有Java代码，可以使用以下语法：
      * **<%=%>**：在 = 的后面编写要输出的内容
      * 这个符号最终被翻译成了这个Java代码：out.print()



### JSP的指令

* 指令的作用：指导JSP的翻译引擎如何工作。（指导当前的JSP翻译引擎如何翻译JSP文件）

* 指令包括哪些？

  * include指令：包含指令，在JSP中完成静态包含，很少用
  * taglib指令：引入标签库的指令（到JSTL标签库的时候再用）
  * page指令：目前重点

* 指令的使用语法：

  * <%@指令名 属性名=属性值 属性名=属性值 属性名=属性值......%>

* page指令中都有哪些常用的属性？

  * ```jsp
    <%@page session="true|false "%>
    true表示启用JSP的内置对象session，表示一定启动session对象，没有session对象会创建。如果没有设置，默认值就是session="true"
    ```

  * ```jsp
    <%@page conteneType="text/json"%>
    contentType属性用来设置响应的内容类型
    ```

  * ```jsp
    <%@page pageEncoding="UTF-8"%>
    pageEncoding属性用来设置响应时采用的字符集。
    和 contentType="text/html;charset=UTF-8" 一个效果   
    ```

  * ```jsp
    <%@page import="java.util.*"%>
    导包
    ```

  * ```jsp
    <%@page erroePage="/error.jsp"%>
    当前页面出问题之后，跳转到erroe.jsp页面
    erroePage属性用来指定出错之后的跳转位置
    ```

  * ```jsp
    <%@page isErrorPage="true"%>
    表示启用JSP九大内置对象之一：exception，默认值是：false
    ```



### JSP的九大内置对象

* jakarta.servlet.jsp.PageContext---->pageContext（页面作用域）

* jakarta.servlet.http.HttpServletRequest---->request（请求作用域）

* jakarta.servlet.http.HttpSession---->session（会话作用域）

* jakarta.servlet.ServletContext---->application（应用作用域）

  * pageContext<request<session<application

  * 以上四个域都有：setAttribute、getAttribute、removeAttribute方法

    

* java.lang.Throwable---->exception

* jakarta.servlet.ServletConfig---->config

* java.lang.Object---->page（其实是this，当前的servelt对象）

  

* jakarta.servlet.jsp.JspWriter---->out（负责输出）

* jakarta.servlet.http.HttpServletResponse---->response（负责响应）

  

---

## EL表达式

* EL表达式是干什么的？

  * Expression Language（表达式语言）
  * EL表达式可以代替JSP文件中的Java代码，让JSP文件中的程序看起来更加整洁，美观
  * JSP中夹杂着各种Java代码，导致JSP文件很混乱，不好维护
  * EL表达式可以算是JSP语法的一部分，EL表达式归属于JSP

* EL表达式在JSP中出现主要是：
  * 从某个域中取数据，然后将其转换成字符串，然后将其输出到浏览器，这就是EL表达式的功效。三大功效：
    * 第一功效：从某个域中取数据
      * 四个域：
        * pageContext
        * request
        * session
        * application
    * 第二功效：将取出的数据转换成字符串
      * 如果是一个Java对象，会自动调用Java对象的toString方法将其转换成字符串。
    * 第三功效：将字符串输出到浏览器
      * 和这个一样：<%= %>，将其输出到浏览器
  
* EL表达式很好用，语法格式：
  * ${表达式}
  
  * EL表达式的使用：
  
    ```jsp
    <%
    User user=new User();
    user.setUsername("jack");
    user.setPassword("123");
    user.setAge(20);
    
    //将User对象存储到某个域中，一定要存，因为EL表达式只能从某个范围中取数据
    //数据是必须存储到四大范围之一的
    request.setAttribute("userObj",user);
    %>
    
    //使用EL表达式
    ${userObj};  //不能加双引号
    //如果添加了双引号，EL表达式会把他当作普通的字符输出到浏览器
    等同于Java代码：<%= request.getAttribute("userObj") == null ? "" : request.getAttribute("userObj"%>
    
    ${userObj}底层的实现：从域中取出数据，取出user对象，然后调用user对象的toString方法，转换成字符串，输出到浏览器
    
    <%--输出对象的属性值--%>
    ${userObj.username}---->使用这个语法的前提是User对象有getUsername()方法
    EL表达式中的 .运算符 这个语法实际上调用了底层的getXxxx()方法
    //写那些get方法时，建议使用驼峰命名法，不然服务器可能会报500错误
    ```
  



* 在没有指定范围的情况下，EL表达式优先从小范围中取数据
  * pageContext<request<session<application

* EL表达式中可以指定范围来读取数据
  * EL表达式有4个隐式的范围对象
  * pageScope  requestScope  sessionScope  applicationScope
  * 实际开发中，因为向某个域中存储数据的时候，name都是不同的。所以xxxScope都是可以省略的



* EL表达式存取数据的时候有两种形式：

  * 第一种：.（大部分使用这种方式）

  * 第二种：[ ]（如果存储到域的时候，这个name含有特殊字符，可以使用[ ]）

    * ```jsp
      request.setAttribute("abc.de","zhangsan")
      
      ${requestScope.adc.de}这样是无法取值的
      应该这样：${requestScope["abc.de"]}
      
      
      ```

* 掌握EL表达式，怎么从Map集合里取数据：

  * ${map.key}

* 掌握EL表达式，怎么从数组中取数据：

  * ${数组[0]}

  * ${数组[1]}

* list集合也是通过下标获取

* page指令中，有一个属性，可以忽略EL表达式

* ```jsp
  <% page contentType="text/html;charset=UTF-8" isELIgnored="true" %>
  isELIgnored="true" 表示忽略EL表达式
  默认值是false
  
  isELIgnored="true"是全局的控制
  
  可以使用反斜杠进行局部控制：\${username} 这样也可以忽略EL表达式
  ```

  

* 通过EL表达式获取应用的根：

  * EL表达式中没有request这个隐式对象，有pageContext这个隐式对象
  * ${pageContext.request.contextPath}

* EL表达式中其他的隐式对象：

  * param----获取单值的请求参数

    * ```jsp
      <%= request.getParameter("参数名")%>
      相当于EL表达式中的：${param.参数名}
      ```

  * paramValues----获取多值的请求参数，以数组形式返回

  * initParam----ServletContext是Servet的上下文对象，对应的JSP九大内置对象之一是：application

* EL表达式的运算符：

  * empty----判断是否为空，如果是空结果为true
  * eq（调用equals方法）
  * == 和  !=（调用equals方法进行判断）
  * ！和 not 都是进行取反

  

  ---

  ## JSTL标签库

  * 什么是JSTL？
    * Java Standard Tag Lib（Java标准的标签库）
    * JSTL标签库通常结合EL表达式，目的是让JSP中的Java代码消失

  * 使用JSTL标签库的步骤：

    * 第一步：引入JSTL标签库对应的jar包

      * 和mysql的数据库驱动一样，都是放在WEB-INF/lib目录下的
      * 如果这个jar包不是tomcat服务器自带的，就需要放到WEB-INF/lib目录下
      * **注意：如果将jar包导入之后还爆红：右击jar包，选择添加为库**

    * 第二步：在JSP中引入要使用的标签库（使用taglib指令来引入标签库）

      * JSTL提供了很多种标签。

      * ```jsp
        <%@ taglib prefix="c"  uri="http://java.sun.com/jsp/jstl/core "%>
        这个就是核心标签库
        prefix="这里随便起个名字就行，默认为c"
        ```

    * 第三步：在需要使用标签的位置使用即可，表面使用的是标签，底层使用的还是Java程序

  

  * JSTL标签的原理：

    * ```jsp
      <%@ taglib prefix="c"  uri="http://java.sun.com/jsp/jstl/core "%>
      以上uri后面的路径实际上指向了一个xxx.tld文件
      tld文件实际上是一个xml配置文件
      在tld文件中描述了“标签”和“java类”之间的关系
      以上的核心标签库对应的tld文件是：c.tld文件
      ```

  * JSTL中的核心库core中有哪些常用的标签

    * c:if

      * <c:if test="boolean类型，支持EL表达式"></c: if>

    * c:forEach

      * <c:forEach items="集合，支持EL表达式" var="集合中的元素" varStatus="元素状态对象"> ${元素状态对象.count} </c: forEach>
      * <c: forEach var="i" begin="1" end="10" step="2">${i} </c: forEach>
  
    * c:choose  c:when  c:otherwise
  
      * ```jsp
        <c:choose>
            <c:when test="${param.age<18}">
            	青少年
            </c:when>
            <c:when test="${param.age<35}">
            	青年
            </c:when>
            <c:when test="${param.age<55}">
            	中年
            </c:when>
            <c:otherwise>
            	老年
            </c:otherwise>
        </c:choose>
        ```
  
  
  
  
  
  ---
  
  ## Filter过滤器
  
  * 当前项目存在的问题
  
    * 每一个Servlet都是处理自己相关的业务，在这些Servet执行之前都需要判断用户是否已经登陆，如果用户登良了可以继续操作，如果没有登录，需要用户登录。这段判断用户是否登录的代码是固定的，并且每一个Servlet类中都需要编写，这些代码都是重复编写，怎么解决？
  
      * 可以使用Servet规范中的Filter过滤器来解决这个问题
  
        * 我们可以把Servlet程序看作是一个最终要执行的目标，我们可以使用过滤器Filter来添加过滤代码，这个过滤代码可以添加到Servlet执行之前也可以添加到Servlet执行之后。
        * 一般情况下，都是咋过滤器中编写公共代码
  
      * 步骤：
  
        * 第一步：编写一个Java类实现一个接口：jarkata.servlet.Filter，并实现这个接口的所有方法
  
          * init方法：在Filter对象第一次被创建之后调用，只调用一次
          * doFilter方法：只要用户发送一次请求，则执行一次。发送N次请求则执行N次。在这个方法中编写过滤规则
          * destory方法：在Filter对象被释放/销毁之前调用，只调用一次
  
        * 第二步：在web.xml文件中对Filter进行配置，和Servlet的配置很像
  
        * ```xml
              <filter>
                  <filter-name></filter-name>
                  <filter-class></filter-class>
              </filter>
              <filter-mapping>
                  <filter-name></filter-name>
                  <uri-partten></uri-partten>
              </filter-mapping>
          ```
  
          * 或者使用注解：@WebFilter({".do"})
  
      * 注意：
  
        * Servlet对象默认情况下，在服务器启动的时候不会新建对象
        * Filter对象默认情况下在服务器启动的时候会新建对象
        * Servlet是单例的，Filter也是单例的
  
  
  
  ![image-20230315214256965](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230315214256965.png)
  
  
  
  
  
  * 目标Servet是否执行，取决于两个条件：
    * 在过滤器中是否编写了：chain.doFilter(request,response);
    * 用户发送的请求路径是否和Servet的 请求路径一直
    
  * chain.doFilter(request,response);这行代码的作用：
    * 执行下一个过滤器，如果下面没有过滤器了，执行最终的Servlet
    
  * 注意：Filter的优先级天生就比Servet的优先级高
    * /a.do对应一个Filter，也对应一个Servlet，那么一定是先执行Filter，再执行Servlet
    
  * 关于FIlter的配置路径：
    * /a.do、/b.do这些都是精确匹配
    * /*匹配所有路径
    * *.do 后缀匹配，不要以 / 开始
    * /dept/* 前缀匹配
    
  * 在web.xml文件中进行配置时，Filter的执行顺序：
    * 依靠filter-mapping标签的配置位置，越靠上优先级越高
    
  * 过滤器的调用顺序：遵循栈数据结构
  
  * 使用@WebFilter的时候，Filter的执行顺序：
    * 比较Filter这个类名
    * 例如：FilterA和FilterB，则先执行FilterA
    * 例如：Filter1和Filter2，则先执行Filter1
    
  * Filter的生命周期：
    * 和Servet对象生命周期一致
    * 唯一的区别：Filter默认情况下，在服务器启动阶段就会初始化，Servlet不会
    
  * Filter有一个设计模式：
    * 责任链设计模式
    
    * 过滤器最大的优点：
      * 在程序编译阶段不会确定调用顺序，因为Filter的调用顺序是配置到web.xml文件中的，只要修改web.xml配置文件中filter-mapping的顺序就可以调整Filter的执行顺序，**显然，Filter的执行顺序是在程序运行阶段动态组合的，这种设计模式称为责任链设计模式**
      
    * 责任链设计模式最大的核心思想：
      * 在程序运行阶段，动态的组合程序的调用顺序
      
        
  
  ---
  
  ## Listener监听器
  
  * 什么是监听器？
  
    * 监听器是Servlet规范的一员，就像Filter一样
    * 在Servlet中，所有的监听器接口都是以“Listener”结尾
  
  * 监听器有什么用？
  
    * 监听器实际上是Servlet规范留给javaweb程序员的特殊时机
    * 特殊的时刻如果想执行这段代码，需要使用相应的监听器
  
  * Servlet规范中提供了哪些监听器？
  
    * jakarta.servlet包下：
      * ServletContextListener
      * ServletContextAttributeListener
      * ServletRequestListener
      * ServletRequestAttributeListener
    * jakarta.servlet.http包下：
      * HttpSessionListener
      * HttpSessionAttributeListener
        * 该监听器需要使用@WebListener注解进行标记
        * 该监听器监听的是session域中数据的变化。只要数据变化，则执行相应的方法。主要监测点在session域对象上
      * HttpSessionBindingListener
        * 该监听器不需要使用@WebListener进行注解
        * 假设User类实现该监听器，那么User对象在被放入session的时候触发bind事件，User对象从session中删除的时候，触发unbind事件
        * 假设Customer类没有实现该监听器，那么Customer对象放入session或者从session中删除的时候，不会触发bind和unbind事件
      * HttpSessionIdListener
        * session的id发生改变的时候，监听器中的唯一一个方法就会被调用
      * HttpSessionActivationListener
        * 监听session对象的钝化和活化
        * 钝化：session对象从内存存储到硬盘文件
        * 活化：从硬盘文件把session恢复到内存
  
  * 实现一个监听器的步骤：以ServletContextListener为例
  
    * 第一步：编写一个类实现ServletContextListener接口，并实现里面的方法
  
    * 第二步：在web.xml文件中对ServletContextListener接进行配置
  
      * ```xml
        <listener>
            <listener-class>包名.类名</listener-class>
        </listener>
        ```
  
      * 也可以不使用配置文件，使用注解即可（@WebListener）
  
  * 注意：所有监听器中的方法都不需要javaweb程序员调用，都由服务器来调用，什么视乎调用？
  
    * 当某个特殊事件发生之后，被web服务器自动调用
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

---

## 使用Servlet+JSP改造项目

* 使用Servlet处理业务收集数据，使用JSP展示数据
* 将之前原型中的html文件全部修改为jsp文件，然后在jsp文件头部添加page指令(指定ContextType防止中文乱码)，将所有的JSP文件拷贝到web目录下
* 完成所有页面的正常流转。(页面能够正常的跳转，修改超链接的请求路径)
  * <%=request.getContextPath() %>：在jsp中动态获取应用的根路径
* Servet中链接数据库，查询所有的部门，遍历结果集
  * 遍历结果集的过程中，取出部门编号、部门名、位置等信息，封装成java对象
  * 将Java对象放在List集合中
  * 将List集合存储到request域当中
  * 转发forward到jsp
* 在JSP中：
  * 从request域中取出List集合
  * 遍历List集合，取出每个部门对象，动态生成tr



* 可以单独使用JSP来完成所有的功能，因为JSP就是Servet，在JSP的<%%>里面写的代码就是在service方法中的。所以<%%>里面完全可以编写JDBC代码，连接数据库，查询数据，也可以在这个方法当中编写业务逻辑代码，处理业务。所以使用单独的JSP开发web应用完全没问题
* 虽然JSP一个技术就可以完成web应用，但不建议，还是建议使用Servlet+JSP的方式进行开发，这样能吧各自优势展示出来，JSP就是做数据展示，Servlet就是做数据收集。(JSP中编写的Java代码越少越好)，一定还要职责分明

* 包名为bean的意思：
  * javabean(java的logo是一杯冒着热气的咖啡，javabean被翻译为：咖啡豆)
  * Java是一杯咖啡，咖啡是由一粒一粒的咖啡店研磨而成的，整个java程序中有很多的bean存在
  * 实际上javabean可以理解为符合某种规范的java类，比如：
    * 有无参数构造方法
    * 属性私有化
    * 对外提供公开的set和get方法
    * 实现了java.io.Serializable接口
    * 重写toString
    * 重写hashCode+equals
  * javabean实际就是Java中的实体类，负责数据的封装



实现登陆页面

* 步骤一：在数据库中添加一个用户登录表：user_login
* 步骤二：实现一个登陆页面
  * 页面要有一个登录的表单，有用户名和密码的输入框
  * 用户点击登录，提交表单，提交用户名和密码，post方法提交
* 步骤三：后台有一个对于的Servlet处理登录的请求
  * 登录成功：跳转到部门列表页面
  * 登陆失败：跳转到失败的页面
* 步骤四：提供一个登录失败的页面