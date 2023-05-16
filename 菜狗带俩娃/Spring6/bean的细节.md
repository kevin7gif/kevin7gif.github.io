### bean的实例化方式

Spring为bean提供了多种实例化方式，包括四种（Spring中为bean对象的创建准备了多种方案，目的是：更加灵活）

* 第一种：通过构造方法实例化
* 第二种：通过简单工厂模式实例化
* 第三种：通过factory-bean实例化
* 第四种：通过FactoryBean接口实例化





### BeanFactory和FactoryBean的区别



**BeanFactory** 

Spring IoC容器的顶级对象，BeanFactory被翻译为“Bean工厂”，在Spring的IoC容器中，“Bean工厂”负责创建Bean对象。

BeanFactory是工厂。



 

**FactoryBean**：它是一个Bean，是一个**能够辅助Spring**实例化其它Bean对象的一个Bean。

在Spring中，Bean可以分为两类：

* 第一类：普通Bean

* 第二类：工厂Bean（记住：工厂Bean也是一种Bean，只不过这种Bean比较特殊，它可以辅助Spring实例化其它Bean对象。）







### Bean的生命周期

**Spring其实就是一个管理Bean对象的工厂**。它负责对象的创建，对象的销毁等。
**生命周期就是：对象从创建开始到最终销毁的整个过程。**
什么时候创建Bean对象？
创建Bean对象的前后会调用什么方法？
Bean对象什么时候销毁？
Bean对象的销毁前后调用什么方法？

生命周期的本质是：在哪个时间节点上调用了哪个类的哪个方法



#### Bean的生命周期之5步

* 第一步：实例化Bean --------> 调用bean的无参数构造方法
* 第二步：Bean属性赋值 --------> 执行set注入
* 第三步：初始化Bean 
* 第四步：使用Bean
* 第五步：销毁Bean





#### Bean生命周期之7步

在以上的5步中，第3步是初始化Bean，如果你还想在初始化前和初始化后添加代码，可以加入“Bean后处理器”。

**注意！！！：将Bean后处理器配置到xml文件后，该后处理器将作用于整个配置文件中的bean**

编写一个类实现BeanPostProcessor类，并且重写before和after方法：

```java
package com.powernode.spring6.bean;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

/**
 * @author 动力节点
 * @version 1.0
 * @className LogBeanPostProcessor
 * @since 1.0
 **/
public class LogBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("Bean后处理器的before方法执行，即将开始初始化");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("Bean后处理器的after方法执行，已完成初始化");
        return bean;
    }
}
```



在xml文件中进行配置

```xml
<!--配置Bean后处理器。这个后处理器将作用于当前配置文件中所有的bean。-->
<bean class="com.powernode.spring6.bean.LogBeanPostProcessor"/>
```



![image.png](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665393936765-0ea5dcdd-859a-4ac5-9407-f06022c498b9.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_29%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)









#### Bean生命周期之10步

增加的三个点位的地方

* 点位1：在 Bean后处理器  before方法之前
  * 检查Bean是否实现了Aware相关的接口，如果实现了，则调用这些接口中的方法
  * 调用这些方法的目的是：传递数据，可以更加方便的使用
* 点位2：在 Bean后处理器  before方法之后
  * 检查Bean是否实现了InitializingBean接口，如果实现了，则调用这些接口中的方法
* 点位3：在使用Bean之后
  * 检查Bean是否实现了DisposableBean接口，如果实现了，则调用这些接口中的方法

在7步的基础上添加的这三个点位的特点：都是在检查Bean是否实现了某些特定的接口，如果实现了这些接口，则Spring容器会都调用这个接口中的方法



![img](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665394697870-15de433a-8d50-4b31-9b75-b2ca7090c1c6.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_12%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



Aware相关的接口包括：BeanNameAware、BeanClassLoaderAware、BeanFactoryAware

* 当Bean实现了BeanNameAware，Spring会将Bean的名字传递给Bean。

* 当Bean实现了BeanClassLoaderAware，Spring会将加载该Bean的类加载器传递给Bean。

* 当Bean实现了BeanFactoryAware，Spring会将Bean工厂对象传递给Bean。

  

* 测试以上10步，可以让User类实现5个接口，并实现所有方法：
  ●BeanNameAware
  ●BeanClassLoaderAware
  ●BeanFactoryAware
  ●InitializingBean
  ●DisposableBean

  



**Spring 根据Bean的作用域来选择管理方式。**

* **对于singleton作用域的Bean，Spring 能够精确地知道该Bean何时被创建，何时初始化完成，以及何时被销毁；**

* **对于prototype作用域的Bean，Spring 只负责创建，当容器创建了 Bean 的实例后，Bean 的实例就交给客户端代码管理，Spring 容器将不再跟踪其生命周期。**
  * 如果在prototype作用域下，对上述代码进行测试，只执行了前8步，第9（**使用bean**）和10（**销毁bean**）都没有执行





### 自己new的对象如何让Spring管理



假设User这个类没有写到xml配置文件中

```java
  @Test
    public void testRegisterBean(){
        //自己new的对象
        User user=new User();
        System.out.println(user);

        //将以上自己new的对象纳入Spring容器来管理
        DefaultListableBeanFactory factory=new DefaultListableBeanFactory();
        factory.registerSingleton("user",user); //自己给这个bean起一个名字，叫user

        //从spring容器中进行获取
        User user1 = factory.getBean("user", User.class);
        System.out.println(user1);
    }
```







### Bean的循环依赖问题



A对象中有B属性。B对象中有A属性。这就是循环依赖。



#### 使用 singleton + setter 模式

新建两个bean类，一个Husband类，一个Wife类

```java
public class Wife {

    private String name;
    private Husband husband;

    @Override
    public String toString() {
        return "Wife{" +
                "name='" + name + '\'' +
                ", husband=" + husband.getName() + //注意，这里重写toString方法时，husband这个属性要调用Husband类的getName()方法，要不然会出现递归导致的栈内存溢出错误。
                '}';
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setHusband(Husband husband) {
        this.husband = husband;
    }
}



public class Husband {

    private String name;
    private Wife wife;

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "Husband{" +
                "name='" + name + '\'' +
                ", wife=" + wife.getName() +  //注意，这里重写toString方法时，wife这个属性要调用Wife类的getName()方法，要不然会出现递归导致的栈内存溢出错误。
                '}';
    }

    public void setWife(Wife wife) {
        this.wife = wife;
    }
}
```



编写xml配置文件

```xml
<!--    singleton + setter模式下的循环依赖 是没有任何问题的 
        singleton表示在整个Spring容器中是单例的，独一无二的对象
    -->
    <bean id="husbandBean" class="com.xiehn.spring6.bean.Husband" scope="singleton">
        <property name="name" value="zhangsan"/>
        <property name="wife" ref="wifeBean"/>
    </bean>

    <bean id="wifeBean" class="com.xiehn.spring6.bean.Wife" scope="singleton">
        <property name="name" value="xiaohua"/>
        <property name="husband" ref="husbandBean"/>
    </bean>
```

**在这种模式下循环依赖不会出现问题，Spring是如何应对的：**

主要的原因是，在这种模式下，Spring对Bean的管理主要分为两个阶段

* 第一个阶段：在Spring容器加载的时候，实例化Bean，只要其中任意一个Bean实例化之后，马上进行“曝光”【不等属性赋值就曝光】
* 第二个阶段：Bean“曝光”之后，再进行属性的赋值（调用set方法）

**核心的解决方案是：实例化对象和对象的属性赋值分为两个阶段来完成**

**只有在scope属性是singleton的情况下，Bean才会采取提前“曝光”的措施**



编写测试类

```java
@Test
    public void testCD(){
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring.xml");
        Husband husbandBean = applicationContext.getBean("husbandBean", Husband.class);
        Wife wifeBean=applicationContext.getBean("wifeBean", Wife.class);
        System.out.println(husbandBean);
        System.out.println(wifeBean);
    }
```







#### 使用prototype + setter 模式

还是上面的程序代码，只将xml配置文件中scope的值改为prototype

```xml
 <bean id="husbandBean" class="com.xiehn.spring6.bean.Husband" scope="prototype">
        <property name="name" value="zhangsan"/>
        <property name="wife" ref="wifeBean"/>
    </bean>

    <bean id="wifeBean" class="com.xiehn.spring6.bean.Wife" scope="prototype">
        <property name="name" value="xiaohua"/>
        <property name="husband" ref="husbandBean"/>
    </bean>
```



在prototype + setter 模式下的循环依赖存在问题，会出现异常

**BeanCurrentlyInCreationException：当前请求的Bean正处于创建中异常**

解决方法：将其中一个scope的属性改为singleton即可







#### 使用构造注入模式

```xml
 <!--构造注入-->
    <bean scope="singleton" class="com.xiehn.spring6.bean2.Husband" id="h">
        <constructor-arg index="0" value="张三"></constructor-arg>
        <constructor-arg index="1" ref="w"></constructor-arg>
    </bean>

    <bean scope="singleton" class="com.xiehn.spring6.bean2.Wife" id="w">
        <constructor-arg index="0" value="小花"></constructor-arg>
        <constructor-arg index="1" ref="h"></constructor-arg>
    </bean>
```

**使用构造注入这种模式产生的循环依赖是无法解决的，所有尽量避免使用**

**主要原因是因为通过构造方法注入导致的：因为构造方法注入会导致实例化对象的过程和对象属性赋值的过程没有分离开，必须在一起完成导致的。**







### Spring解决循环依赖的机理

Spring为什么可以解决set + singleton模式下循环依赖？

根本的原因在于：这种方式可以做到将“实例化Bean”和“给Bean属性赋值”这两个动作分开去完成。
实例化Bean的时候：调用无参数构造方法来完成。此时可以先不给属性赋值，可以提前将该Bean对象“曝光”给外界。
给Bean属性赋值的时候：调用setter方法来完成。

**两个步骤是完全可以分离开去完成的，并且这两步不要求在同一个时间点上完成。**

也就是说，Bean都是单例的，我们可以先把所有的单例Bean实例化出来，放到一个集合当中（我们可以称之为缓存），所有的单例Bean全部实例化完成之后，以后我们再慢慢的调用setter方法给属性赋值。这样就解决了循环依赖的问题。



Spring框架底层的源码：

![img](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665456331018-18c45ae3-fa4c-4cd8-aabf-d9bace567693.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_41%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

在以上类中包含三个重要的属性：

Cache of **singleton** **objects**: bean name to bean instance. 单例对象的缓存：key存储bean名称，value存储Bean对象【一级缓存】

Cache of **early singleton objects**: bean name to bean instance. 早期单例对象的缓存：key存储bean名称，value存储早期的Bean对象【二级缓存】

Cache of **singleton factories**: bean name to ObjectFactory. 单例工厂缓存：key存储bean名称，value存储该Bean对应的ObjectFactory对象【三级缓存】



**一级缓存**存储的是：单例Bean对象。完整的单例Bean对象，也就是说这个缓存中的Bean对象的属性都已经赋值了。是一个完整的Bean对象

**二级缓存**存储的是：早期的单例Bean对象。这个缓存中的单例Bean对象的属性没有赋值。只是一个早期的实例对象

**三级缓存**存储的是：单例工厂对象。这个里面存储了大量的“工厂对象”，每一个单例Bean对象都会对应一个单例工厂对象。

​								这个集合中存储的是，创建该单例对象时对应的那个单例工厂对象



**三个缓存本质上是Map集合**

该类中有这样一个方法addSingletonFactory()，这个方法的作用是：将创建Bean对象的ObjectFactory对象提前曝光。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665460724682-2222366d-cc07-43db-a8d0-fb27712b20a4.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_31%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)





![image.png](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665460240687-3d0794c4-e6ed-4653-9463-767a7f943ff9.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_5Yqo5Yqb6IqC54K5%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1020%2Climit_0)



**Spring只能解决setter方法注入的单例bean之间的循环依赖。ClassA依赖ClassB，ClassB又依赖ClassA，形成依赖闭环。Spring在创建ClassA对象后，不需要等给属性赋值，直接将其曝光到bean缓存当中。在解析ClassA的属性时，又发现依赖于ClassB，再次去获取ClassB，当解析ClassB的属性时，又发现需要ClassA的属性，但此时的ClassA已经被提前曝光加入了正在创建的bean的缓存中，则无需创建新的的ClassA的实例，直接从缓存中获取即可。从而解决循环依赖问题。**