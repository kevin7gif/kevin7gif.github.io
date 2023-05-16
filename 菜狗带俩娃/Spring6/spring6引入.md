### OCP开闭原则

开闭原则：在软件开发过程中应当对扩展开放，对修改关闭。

也就是说，如果在进行功能扩展的时候，添加额外的类是没问题的，但因为功能扩展而修改之前运行正常的程序，这是忌讳的。因为一旦修改之前运行正常的程序，就会导致项目整体要进行全方位的重新测试。这是相当麻烦的过程。导致以上问题的主要原因是：代码和代码之间的耦合度太高。



![image.png](https://cdn.nlark.com/yuque/0/2022/png/21376908/1663658802926-4c783887-3bd3-4a35-b32a-b2cd57d0061c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_18%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

可以很明显的看出，**上层是依赖下层的**。UserController依赖UserServiceImpl，而UserServiceImpl依赖UserDaoImplForMySQL，这样就会导致**下面只要改动，上面必然会受牵连**（跟着也会改），所谓牵一发而动全身。这样也就同时违背了另一个开发原则：依赖倒置原则。







### 依赖倒置原则DIP

依赖倒置原则(Dependence Inversion Principle)，简称DIP

主要倡导面向抽象编程，面向接口编程，不要面向具体编程，**让上层不再依赖下层**，下面改动了，上面的代码不会受到牵连。这样可以大大降低程序的耦合度，耦合度低了，扩展力就强了，同时代码复用性也会增强。（**软件七大开发原则都是在为解耦合服务**）

![img](https://cdn.nlark.com/yuque/0/2023/png/27467446/1677292470693-9a964ed2-f921-4245-a2a1-edee8c846de3.png)

上述代码虽然已经面向接口编程了，但对象的创建是：new UserDaoImplForOracle()显然并没有完全面向接口编程，还是使用到了具体的接口实现类。



什么叫做完全面向接口编程？什么叫做完全符合依赖倒置原则呢？请看以下代码：



如果代码是这样编写的，才算是完全面向接口编程，才符合依赖倒置原则。

那你可能会问，这样userDao是null，在执行的时候就会出现空指针异常呀。所以我们要解决这个问题。解决空指针异常的问题，其实就是解决两个核心的问题：

* 第一个问题：谁来负责对象的创建。【也就是说谁来：new UserDaoImplForOracle()/new UserDaoImplForMySQL()】
* 第二个问题：谁来负责把创建的对象赋到这个属性上。【也就是说谁来把上面创建的对象赋给userDao属性】
* 如果我们把以上两个核心问题解决了，就可以做到既符合OCP开闭原则，又符合依赖倒置原则。



---

在Spring框架中，它可以帮助我们new对象，并且它还可以将new出来的对象赋到属性上。

换句话说，**Spring框架可以帮助我们创建对象，并且可以帮助我们维护对象和对象之间的关系。**



Spring可以new出来UserDaoImplForMySQL对象，也可以new出来UserDaoImplForOracle对象，并且还可以让new出来的dao对象和service对象产生关系（产生关系其实本质上就是给属性赋值）。

很显然，这种方式是将对象的创建权/管理权交出去了，不再使用硬编码的方式了。同时也把对象关系的管理权交出去了，也不再使用硬编码的方式了。像这种把对象的创建权交出去，把对象关系的管理权交出去，被称为**控制反转**。





### 控制反转IoC

控制反转（Inversion of Control，缩写为IoC），是面向对象编程中的一种设计思想，可以用来降低代码之间的耦合度，符合依赖倒置原则。

控制反转的核心是：**将对象的创建权交出去，将对象和对象之间关系的管理权交出去，由第三方容器来负责创建与维护。**

**反转的两件事**：

* 不在程序中采用硬编码的方式来new对象了（new对象的权力交出去了）
* 不在程序中采用硬编码的方式来维护对象的关系了（对象之间关系的维护权也交出去了）

控制反转常见的实现方式：依赖注入（Dependency Injection，简称DI）

**Spring通过依赖注入的方式来完成Bean管理**

**Bean管理：Bean对象的创建以及Bean对象中属性的赋值（或者叫做Bean对象之间关系的维护）**

依赖注入：

* 对象A和对象B之间的关系，靠注入的手段来维护
* 注入指的是一种数据传递行为，通过注入行为来让对象和对象产生关系

依赖注入的实现由包括两种方式：

* set方法注入（执行set方法给属性赋值）
* 构造方法注入（执行构造方法给属性赋值）

**依赖**：A对象和B对象的关系

**注入**：一种手段，通过这种手段，可以让A对象和B对象产生关系

而**Spring框架就是一个实现了IoC思想的框架**。

* 可以帮你new对象（实现原理：通过反射机制，调用类的无参数构造方法来实例化对象）
* 可以帮你维护对象和对象之间的关系

IoC可以认为是一种全新的设计模式，但是理论和时间成熟相对较晚，并没有包含在GoF中。（GoF指的是23种设计模式）

**IoC是思想，依赖注入是实现**





### Spring八大模块

![image-20230402151508241](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230402151508241.png)

1.Spring Core模块

这是Spring框架最基础的部分，他提供了依赖注入（DI）特征来实现容器对Bean的管理。核心容器的主要组件是 BeanFactory，BeanFactory是工厂模式的一个实现，是任何Spring应用的核心。它使用IoC将应用配置和依赖从实际的应用代码中分离出来。

2.Spring Context模块
如果说核心模块中的BeanFactory使Spring成为容器的话，那么上下文模块就是Spring成为框架的原因。
这个模块扩展了BeanFactory，增加了对国际化（I18N）消息、事件传播、验证的支持。另外提供了许多企业服务，例如电子邮件、JNDI访问、EJB集成、远程以及时序调度（scheduling）服务。也包括了对模版框架例如Velocity和FreeMarker集成的支持

3.Spring AOP模块
Spring在它的AOP模块中提供了对面向切面编程的丰富支持，Spring AOP 模块为基于 Spring 的应用程序中的对象提供了事务管理服务。通过使用 Spring AOP，不用依赖组件，就可以将声明性事务管理集成到应用程序中，可以自定义拦截器、切点、日志等操作。

4.Spring DAD模块
提供了一个JDBC的抽象层和异常层次结构，消除了烦琐的JDBC编码和数据库厂商特有的错误代码解析，用于简化JDBC。

5.Spring ORM模块
Spring提供了ORM模块。Spring并不试图实现它自己的ORM解决方案，而是为几种流行的ORM框架提供了集成方案，包括Hibernate、JDO和iBATIS SQL映射，这些都遵从 Spring 的通用事务和 DAO 异常层次结构。

6.Spring Web MVC模块
Spring为构建Web应用提供了一个功能全面的MVC框架。虽然Spring可以很容易地与其它MVC框架集成，例如Struts，但Spring的MVC框架使用IoC对控制逻辑和业务对象提供了完全的分离。

7.Spring WebFlux模块
Spring Framework 中包含的原始 Web 框架 Spring Web MVC 是专门为 Servlet API 和 Servlet 容器构建的。反应式堆栈 Web 框架 Spring WebFlux 是在 5.0 版的后期添加的。它是完全非阻塞的，支持反应式流(Reactive Stream)背压，并在Netty，Undertow和Servlet 3.1+容器等服务器上运行。


8.Spring Web模块
Web 上下文模块建立在应用程序上下文模块之上，为基于 Web 的应用程序提供了上下文，提供了Spring和其它Web框架的集成，比如Struts、WebWork。还提供了一些面向服务支持，例如：实现文件上传的multipart请求。





### Spring特点

1.轻量
从大小与开销两方面而言Spring都是轻量的。完整的Spring框架可以在一个大小只有1MB多的JAR文件里发布。并且Spring所需的处理开销也是微不足道的。
Spring是非侵入式的：Spring应用中的对象不依赖于Spring的特定类。

2.控制反转
Spring通过一种称作控制反转（IoC）的技术促进了松耦合。当应用了IoC，一个对象依赖的其它对象会通过被动的方式传递进来，而不是这个对象自己创建或者查找依赖对象。你可以认为IoC与JNDI相反——不是对象从容器中查找依赖，而是容器在对象初始化时不等对象请求就主动将依赖传递给它。

3.面向切面
Spring提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务（例如审计（auditing）和事务（transaction）管理）进行内聚性的开发。应用对象只实现它们应该做的——完成业务逻辑——仅此而已。它们并不负责（甚至是意识）其它的系统级关注点，例如日志或事务支持。

4.容器
Spring包含并管理应用对象的配置和生命周期，在这个意义上它是一种容器，你可以配置你的每个bean如何被创建——基于一个可配置原型（prototype），你的bean可以创建一个单独的实例或者每次需要时都生成一个新的实例——以及它们是如何相互关联的。然而，Spring不应该被混同于传统的重量级的EJB容器，它们经常是庞大与笨重的，难以使用。

5.框架
Spring可以将简单的组件配置、组合成为复杂的应用。在Spring中，应用对象被声明式地组合，典型地是在一个XML文件里。Spring也提供了很多基础功能（事务管理、持久化框架集成等等），将应用逻辑的开发留给了你。

所有Spring的这些特征使你能够编写更干净、更可管理、并且更易于测试的代码。它们也为Spring中的各种模块提供了基础支持。





---

### Spring创建对象的原理

**spring是通过调用类的无参数构造方法来创建对象的，所以要想让spring给你创建对象，必须保证无参数构造方法是存在的。**

```java
// dom4j解析beans.xml文件，从中获取class的全限定类名
// 通过反射机制调用无参数构造方法创建对象
Class clazz = Class.forName("com.powernode.spring6.bean.User");
Object obj = clazz.newInstance();
```



![image-20230402165533378](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230402165533378.png)





**spring配置文件的名字是随意的，可以有多个**

**spring配置文件中配置的类也可以是JDK中的类，只要这个类不是抽象的，并且提供了无参数构造方法**

例如：

```xml
spring.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userBean" class="com.powernode.spring6.bean.User"/>
    <!--<bean id="userBean" class="com.powernode.spring6.bean.Vip"/>-->

    <bean id="dateBean" class="java.util.Date"/>
</beans>
```



写一个测试类

```java
package com.powernode.spring6.test;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Spring6Test {

    @Test
    public void testFirst(){
        // 初始化Spring容器上下文（解析beans.xml文件，创建所有的bean对象）
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml","spring.xml");

        // 根据id获取bean对象
        Object userBean = applicationContext.getBean("userBean");
        Object vipBean = applicationContext.getBean("vipBean");
        Object dateBean = applicationContext.getBean("dateBean");

        System.out.println(userBean);
        System.out.println(vipBean);
        System.out.println(dateBean);
    }
}
```



![image-20230402165926470](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230402165926470.png)





### BeanFactory

Bean工厂，就是能够生产Bean对象的一个工厂对象

BeanFactory是IoC容器的顶级接口

Spring的IoC容器实际上使用了工厂模式

Spring底层的IoC是如何实现的：XML解析+工厂模式+反射机制