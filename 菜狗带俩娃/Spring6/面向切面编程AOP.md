IoC使软件组件松耦合。AOP让你能够捕捉系统中经常使用的功能，把它转化成组件。

AOP（Aspect Oriented Programming）：面向切面编程，面向方面编程。（AOP是一种编程技术）
AOP是对OOP的补充延伸。
AOP底层使用的就是动态代理来实现的。

Spring的AOP使用的动态代理是：JDK动态代理 + CGLIB动态代理技术。Spring在这两种动态代理中灵活切换，如果是代理接口，会默认使用JDK动态代理，如果要代理某个类，这个类没有实现接口，就会切换使用CGLIB。当然，你也可以强制通过一些配置让Spring只使用CGLIB。





AOP介绍

一般一个系统当中都会有一些系统服务，例如：日志、事务管理、安全等。这些系统服务被称为：**交叉业务**
这些**交叉业务**几乎是通用的，不管你是做银行账户转账，还是删除用户数据。日志、事务管理、安全，这些都是需要做的。
如果在每一个业务处理过程当中，都掺杂这些交叉业务代码进去的话，存在两方面问题：

* 第一：交叉业务代码在多个业务流程中反复出现，显然这个交叉业务代码没有得到复用。并且修改这些交叉业务代码的话，需要修改多处。
* 第二：程序员无法专注核心业务代码的编写，在编写核心业务代码的同时还需要处理这些交叉业务。
* 
* 使用AOP可以很轻松的解决以上问题。
  请看下图，可以帮助你快速理解AOP的思想：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665732609757-d8ae52ba-915e-49cf-9ef4-c7bcada0d601.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_25%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

**用一句话总结AOP：将与核心业务无关的代码独立的抽取出来，形成一个独立的组件，然后以横向交叉的方式应用到业务流程当中的过程被称为AOP。**

**AOP的优点：**

* **第一：代码复用性增强。**
* **第二：代码易维护。**
* **第三：使开发者更关注业务逻辑。**













### AOP的七大术语

```java
public class UserService{
    public void do1(){
        System.out.println("do 1");
    }
    public void do2(){
        System.out.println("do 2");
    }
    public void do3(){
        System.out.println("do 3");
    }
    public void do4(){
        System.out.println("do 4");
    }
    public void do5(){
        System.out.println("do 5");
    }
    // 核心业务方法
    public void service(){
        //连接点
        do1();   //切点
        //连接点
        do2();   //切点
        //连接点
        do3();   //切点
        //连接点
        do5();   //切点
        //连接点
    }
}
```



* **连接点   Joinpoint**
  * 在程序的整个执行流程中，**可以织入**切面的位置。方法的执行前后，异常抛出之后等位置。
  * 描述的是位置

* **切点    Pointcut**
  * 在程序执行流程中，**真正织入**切面的方法
  * 本质上描述的是方法

* **通知    Advice**
  * 又叫增强，就是具体你要织入的代码
  * 例如：具体的事务代码，日志代码，统计时长的代码，**具体的代码就是通知**
  * 通知包括：
    * 前置通知
    * 后置通知
    * 环绕通知
    * 异常通知
    * 最终通知

* **切面     Aspect**
  * **切点 + 通知**

* 织入    Weaving
  * 把通知应用到目标对象上的过程。

* 代理对象
  * 一个目标对象被织入通知后产生的新对象

* 目标对象
  * 被织入通知的对象

请看下图：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665735638342-44194599-66e2-4c02-a843-8a8b3ba5b0c8.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_17%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)





### 切点表达式

定义通知往哪些方法上切入

```java
//语法格式
execution([访问控制权限修饰符] 返回值类型 [全限定类名]方法名(形式参数列表) [异常])
```

访问控制权限修饰符：
	 可选项。
	 没写，就是4个权限都包括。
	 写public就表示只包括公开的方法。
返回值类型：
	  必填项。
	  * 表示返回值类型任意。
	全限定类名：
	 可选项。
	 两个点“..”代表当前包以及子包下的所有类。
	 省略时表示所有的类。
	方法名：
	    必填项。
	  * 表示所有方法。
	      set*表示所有的set方法。
	      形式参数列表：
	       必填项
	       () 表示没有参数的方法
	       (..) 参数类型和个数随意的方法
	       (*) 只有一个参数的方法
	       (*, String) 第一个参数类型随意，第二个参数是String的。
	      异常：
	      可选项。
	      省略时表示任意异常类型。







### 使用Spring的AOP

Spring对AOP的实现包括以下3种方式：

* **第一种方式：Spring框架结合AspectJ框架实现的AOP，基于注解方式**。
* **第二种方式：Spring框架结合AspectJ框架实现的AOP，基于XML方式**。
* 第三种方式：Spring框架自己实现的AOP，基于XML配置方式。



先在pom.xml中引入依赖

```xml
<!--spring context依赖-->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>6.0.0-M2</version>
</dependency>

<!--spring aspects依赖-->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>6.0.0-M2</version>
</dependency>
```

在spring配置文件中添加context命名空间和aop命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

</beans>
```





#### AOP基于注解

先定义目标类

```java
package com.xiehn.spring6.service;

import org.springframework.stereotype.Service;

@Service("userService")
public class UserService { //目标类


    public void login(){ //目标方法
        System.out.println("系统正在进行身份认证。。。");
    }

    public void logout(){
        System.out.println("退出系统！！！");
    }
}
```



定义切面类，在其中添加通知和切点表达式

```java
package com.xiehn.spring6.service;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component("logAspect")
@Aspect //切面类需要用@Aspect注解进行标注
public class LogAspect { //切面

    // 切面=切点 + 通知
    //通知就是增强，就是具体编写的增强代码.


    //前置通知
    @Before("execution(* com.xiehn.spring6.service..*(..))")
    public void beforeAdvice(){
        System.out.println("前置增强代码执行了！！！");
    }

    //后置通知
    @AfterReturning("execution(* com.xiehn.spring6.service..*(..))")
    public void afterReturningAdvice(){
        System.out.println("后置增强代码执行了！！！");
    }

    //环绕通知
    @Around("execution(* com.xiehn.spring6.service..*(..))")
    public void aroundAdvice(ProceedingJoinPoint joinPoint) throws Throwable {
        //前面的代码
        System.out.println("前环绕执行了！！！");
        //执行目标
        joinPoint.proceed();
        //后面的代码
        System.out.println("后环绕执行了！！！");
    }

    //最终通知
    @After("execution(* com.xiehn.spring6.service..*(..))")
    public void afterAdvice(){
        System.out.println("最终通知了！！！");
    }
}
```



将目标类和切面类都纳入spring bean管理：**即在类上添加@Component注解**



在spring配置文件中开启组件扫描和自动代理

```xml
 <!--进行组件扫描-->
    <context:component-scan base-package="com.xiehn.spring6.service"/>

    <!--开启aspectj的自动代理-->
    <!--spring容器在扫描类的时候，查看该类上是否有@Aspectj注解。如果有，则给这个类生成代理对象-->
    <!--
        proxy-target-class="true"    表示强制使用CGLIB动态代理
        proxy-target-class="false"   这是默认值，表示接口使用JDK动态代理
    -->
    <aop:aspectj-autoproxy proxy-target-class="true"/>
```



编写测试程序

```java
package com.xiehn.spring6.test;

import com.xiehn.spring6.service.OrderService;
import com.xiehn.spring6.service.UserService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SpringAOPTest {

    @Test
    public void testBefore(){
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring.xml");

        UserService userService = applicationContext.getBean("userService", UserService.class);
        userService.login();
        userService.logout();
}

```





### 执行顺序

​	**环绕开始**

​	**前置通知**

​	**目标执行**

​	**后置通知**

​	**最终通知**

​	**环绕通知结束**



**当出现异常后：** **后置通知**和**环绕通知的结束部分**不会执行

​	环绕开始

​	前置通知

​	目标执行

​	**异常通知**

​	**最终通知**



如果多个切面的话，顺序如何控制：**可以使用@Order注解来标识切面类，为@Order注解的value指定一个整数型的数字，数字越小，优先级越高。**







### 全注解式开发AOP

就是编写一个类，在这个类上使用大量注解来代替spring的配置文件，spring配置文件就消失了

例如：

```java
package com.powernode.spring6.service;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration //代替spring.xml文件
@ComponentScan("com.powernode.spring6.service")  //组件扫描
@EnableAspectJAutoProxy(proxyTargetClass = true)  //启用自动代理
public class Spring6Configuration {
}
```



测试文件

```java
@Test
public void testAOPWithAllAnnotation(){
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(Spring6Configuration.class);
    OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
    orderService.generate();
}
```









### 基于XML配置方式的AOP（了解）
