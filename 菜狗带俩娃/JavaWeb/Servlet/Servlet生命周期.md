### Servlet对象的生命周期

* 什么是servlet对象的生命周期
  * servlet对象什么时候被创建
  * servlet对象什么时候被销毁
  * servlet对象创建了几个
  * servlet对象的生命周期表示：一个对象从出生到最后死亡，整个过程是怎样的

* servlet对象由谁来维护？
  * servlet对象的创建，方法的调用，对象的销毁都与javaweb程序无关
  * 对象的生命周期由Tomcat服务器（WEB Server）负责
  * Tomcat服务器又称为：WEB容器
  * WEB容器就是用来管理servlet对象的死活

* **自己new的servlet对象不受WEB容器管理**
* WEB容器创建的servlet对象都会放到一个集合中（HashMap），只要在这个集合中的Servlet才能被WEB容器管理，自己new的对象不会被WEB容器管理
* WEB容器底层有一个HashMap集合，这个集合中存储了Servlet对象和请求路径之间的关系
* ![image-20230219204058853](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230219204058853.png)



服务器启动的时候，Servlet对象有没有被创建？

* 在Servlet中提供一个无参数的构造方法，启动服务器时，看构造方法是否执行？
* 经过测试，默认情况下，服务器在启动时，Servlet对象并不会被实例化

怎么让服务器启动的时候创建Servlet对象

* 在web.xml文件中的<Servlet>代码段中加入如下代码

* ```xml
  <load-on-startup>整数</load-on-startup>
  <!--其中，整数代表创建的优先级，数字越小优先级越高。0最高-->
  ```

* 

Servlet对象生命周期

* 默认情况下服务器启动时test1对象并没有实例化

* 用户发送第一次请求时，控制台输出以下内容

* ```
  test1的无参数构造方法执行
  test1's init method execute
  test1's service method execute
  ```

* 根据以上内容得出：

* 用户在发送第一次请求的时候，Servlet对象被实例化（test1的构造方法被执行，并且执行的是无参构造方法）

* test对象被创建出来之后，Tomcat服务器马上调用了test1对象init方法（init方法在执行的时候，test1对象已经存在了，已经被创建出来了）

* 用户发送第一次请求的时候，init方法执行之后，Tomcat服务器马上调用test1对象的service方法

* 用户发送第二次请求的时候：控制台输出

* ```
  test1's service method execute
  ```

* 根据以上内容得出：用户在发生第二次第三次....请求的时候，test1对象并没有创建，还是使用之前的test1对象，直接调用test1对象的service方法，这说明

  * 第一：Servlet对象时单例的（单实例的，但要注意：Servlet对象是单实例的，但是Servlet类并不符合单例模式，称为假单例，之所以单例是因为Servlet对象的创建不由javaweb程序管，由Tomcat管，Tomcat只创建了一个，所以导致单例，真单例是指构造方法私有化）
  * 第二：无参构造方法，init方法只在用户发送第一次请求的时候执行，也就是说init方法只被Tomcat服务器调用一次，无参数构造方法只执行一次
  * 第三：只要用户发送一次请求，service方法必然会被Tomcat服务器调用一次

* 关闭服务器的时候，控制台输出

* ```
  test1's destroy method execute
  ```

* 通过以上内容，得出：

* Servlet的destroy方法只被Tomcat服务器调用一次

* destroy方法什么时候被调用？

  * 在服务器关闭的时候
  
  * 因为服务器关闭的时候要销毁test1对象的内存
  
  * 服务器在销毁test1对象内存之前，Tomcat服务器会自动调用test1对象的destroy方法，关闭一些资源，比如：流、数据库连接等
  
    

* Servlet对象像人的一生
  * Servlet对象的无参数构造方法执行：标志着你已经出生
  * Servlet对象的init方法执行：标志着你正在接受教育
  * Servlet对象的service方法执行：标志着你已经开始工作，为人类提供服务
  * Servlet对象的destroy方法执行：标志着临终

* **当Servlet类中编写了一个有参数的构造方法，如果没有手动编写无参数构造方法就会**报错
  * 500错误
  * 500是一个HTTP协议的错误状态码
  * 500一般情况是因为服务器端的java程序出现异常

* Servlet规范中有要求，作为javaweb程序员，编写Servlet类的时候，不建议手动编写构造方法。因为编写构造方法很容易让无参数的构造方法消失，这个操作可能会导致Servlet对象无法实例化，所以init方法存在是有必要的



* service方法使用的最多，这个方法是一定要实现的，因为service方法是处理用户请求的核心方法
* init方法很少用，通常在init方法中做初始化操作，并且这个操作只执行一次，例如：初始化数据库连接池，初始化线程池等
* destroy方法也很少用，通常在该方法中进行资源的关闭，马上对象要被销毁了，还有什么么有关闭的，抓紧时间关闭资源，还有什么资源没有保存到，抓紧时间保存



我们编写一个Servlet类直接实现Servlet接口的话，只需要service方法，其他大部分方法是不需要的，代码很丑陋，可以设计一个**适配器模式Adapter**

* 编写一个GenericServlet类，将该类定义为抽象类，其中有一个抽象方法service
* GenericServlet类实现Servlet接口
* GenericServlet是一个适配器
* 以后编写的所有Servlet类继承GenericServlet类，重写service方法即可
  * 虽然在GenericServlet类中并没有将init方法定义为抽象方法，但是init方法还是会执行的，由Tomcat服务器执行，init方法重点ServletConfig对象也是Tomcat传过来的
  * Tomcat服务器先创建了ServletConfig对象，然后调用init方法，将ServletConfig对象传给了init方法

以下是Tomcat服务器的伪代码

```java
public class Tomcat{
    public static void main(String[] args){
        //Tomcat服务器伪代码
        //创建类对象（通过反射机制调用无参数构造方法来实例化类对象）
        Class clazz=Class.forName("类路径");
        Object obj=clazz.newInstance();
        
        //向下转型
        Servlet servlet=(Servlet)obj;
        
        //创建ServletConfig对象
        //Tomcat服务器负责将ServletConfig对象实例化出来
        ServletConfig servletConfig=new org.apache.catalina.core.StandardwrapperFacade();
        
        //调用Servlet的init方法
        servlet.init(servletConfig);
        
        //调用Servlet的service方法
    }
}
```

**实际应用中，写的Servlet类不用实现Servlet接口，直接继承GenericServlet（Jakarta中已经写好了的）这个类即可（详情见idea工具下的JavaWeb项目下的Servlet2模块）**

---

**ServletConfig**

* Servlet对象的配置信息对象
* ServletConfig对象中封装了<servlet></servlet>标签中的配置信息（web.xml文件中的配置信息）
* 一个Servlet对应一个ServletConfig对象
* ervlet对象是Tomcat服务器实现的，ServletConfig对象也是TomCat服务器创建的，默认情况下，他们都是在用户第一次发送请求的时候创建
* TomCat服务器调用Servlet对象的init方法时需要传一个ServletConfig对象的参数给init方法
* ServletConfig接口的实现类是Tomcat服务器实现的（WEB服务器）

* **ServletConfig接口中的方法都可以在Servlet类中用this.调用，因为GenericServlet类已经实现了ServletConfig接口**

---

### ServletContext

* 一个Servlet对象对应一个ServletConfig，100个Servlet对象对应100个ServletConfig，
* **只要在同一个webapp中，只要在同一个应用中，所有Servlet对象都共享同一个ServletContext对象**
* ServletContext对象在服务器启动时创建，在服务器关闭时销毁，这就是他的生命周期，是应用级对象
* **ServletContext被称为Servlet上下文对象，一个ServletContext对象通常对应一个web.xml文件**
* ServletContext是一个接口，由Tomcat服务器创建，在启动webapp的时候创建

* ServletContext接口中的常用方法：

* ```java
  public String getInitParameter(String name);//通过初始化参数的name获取value
  public Enumeration<String> getInitParameterNames();//获取所有的初始换参数的name
  ```

* ```java
  //获取应用的根路径这个方法很重要，因为在java代码中有一些地方可能会用到应用的根路径，这个方法可以动态获取应用的根路径
  //在Java源码中，不要将应用的根路径写死，因为应用在最终部署时名字可能会发生变化
  public String getContextPath();//动态获取应用上下文的根（应用的根路径）
  ```

* ```java
  public String getRealPath(String path);//获取文件的绝对路径
  //后面的这个路径，加了一个"/"，这个"/"表示当前web的根，不加"/"也可以，默认也是从根下开始找
  ```

* ```java
  //通过ServletContext对象也可以记录日志
  public void log(String msg);
  public void log(String msg,Throwable t);
  //日志会自动记录到CATALINA_HOME/logs目录下
  //如果用idea工具的话，则会在idea目录下tomcat服务器的日志里面记录
  Tomcat服务器的logs目录下的文件类型：
      catalina.2023-02-21.log//服务器端的java程序运行的控制台信息
      localhost.2023-02-21.log//ServletContext对象的log方法记录的日志信息存储到这个文件
      localhost_access_log.2023-02-21.txt//访问日志
  ```

* ```java
  /*
  	ServletContext对象还有一个名字：应用域（后面还会有：请求域，会话域等）
  	如果所有用户共享一份数据，并且这个数据很少被修改，而且数据量很少，可以将这些数据放到ServletContext这个应用域中
  	之所以数据量要小，是因为如果数据量比较大的话，太占用堆内存，并且这个对象的生命周期比较长，服务器关闭的时候，这个对象才会被销毁，大数据量会影响服务器的性能。
  	之所以这些共享数据很少修改或者不修改是因为所有用户共享的数据如果涉及到修改操作，必然会存在线程并发所带来的安全问题，所有ServletContext对象中的数据一般都是只读的
  	数据量小，所有用户共享，又不修改，这样的数据放到ServletContext中，会大大提高效率，因为应用域相当于一个缓存，放到缓存中的数据下次用的时候就不要在数据库中获取，提高执行效率
  */
  
  //存（怎么向应用域中存数据）
  public void setAttribute(String name,Object value);
  //取（怎么取应用域中的数据）
  public Object getAttribute(String name);
  //删（怎么删除应用域中的数据）
  public void removeAttribute(String name);
  ```



* **以后编写Servlet类，实际上是不会直接继承GenericServlet类的，因为B/S的系统，这种系统是基于HTTP超文本传输协议的，在Servlet规范中，提供了一个类叫HttpServlet，它是专门为HTTP协议准备的一个Servlet类，我们编写的Servlet类要继承HttpServlet（HttpServlet是HTTP协议专用的）使用HttpServlet处理更便捷**



```java
jakarta.servlet.Servlet（接口）【爷爷】
jakarta.servlet.GenericServlet implements Servlet（抽象类）【儿子】
jakarta.servlet.http.HttpServlet extends GenericServlet(抽象类)【孙子】
//我们以后编写的Servlet直接继承HttpServlet类即可
```

