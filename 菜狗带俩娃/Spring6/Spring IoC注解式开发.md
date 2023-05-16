**注解的存在主要是为了简化XML的配置。Spring6倡导全注解开发。**



```java
package com.powernode.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(value = {ElementType.TYPE})
@Retention(value = RetentionPolicy.RUNTIME)
public @interface Component {
    String value();
}
```

以上是自定义了一个注解：Component

该注解上面修饰的注解包括：Target注解和Retention注解，这两个注解被称为**元注解**。
Target注解用来设置Component注解可以出现的位置，以上代表表示Component注解只能用在类和接口上。
Retention注解用来设置Component注解的保持性策略，以上代表Component注解可以被反射机制读取。
String value()：是Component注解中的一个属性。该属性类型String，属性名是value。

语法格式：@注解类型名(属性名=属性值, 属性名=属性值, 属性名=属性值......)

```java
package com.powernode.bean;

import com.powernode.annotation.Component;

//@Component(value = "userBean")，若注解中属性的值是value，则value可以省略
@Component("userBean")
public class User {
}
```





例如：假设我们现在只知道包名：com.powernode.bean。至于这个包下有多少个Bean我们不知道。哪些Bean上有注解，哪些Bean上没有注解，这些我们都不知道，如何通过程序全自动化判断？

```java
package com.powernode.test;

import com.powernode.annotation.Component;

import java.io.File;
import java.net.URL;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.Map;

public class Test {
    public static void main(String[] args) throws Exception {
        // 存放Bean的Map集合。key存储beanId。value存储Bean。
        Map<String,Object> beanMap = new HashMap<>();

        String packageName = "com.powernode.bean";
        //开始扫描程序
        //在正则表达式中 \\.  表示一个普通字符 .
        String path = packageName.replaceAll("\\.", "/");
        //com是在类的根路径下的一个目录，调用类加载器的方法，拿到绝对路径
        URL url = ClassLoader.getSystemClassLoader().getResource(path);
        //获取一个绝对路径下的所有文件
        File file = new File(url.getPath());
        //listFiles方法是获取所有的子文件
        File[] files = file.listFiles();
        //对得到的数组进行遍历，每一个元素就是具体的类
        Arrays.stream(files).forEach(f -> {
            //将类和包名进行拼接，得到类名
            String className = packageName + "." + f.getName().split("\\.")[0];
            try {
                //通过反射机制进行注解
                Class<?> clazz = Class.forName(className);
                //判断类上是否有这个注解
                if (clazz.isAnnotationPresent(Component.class)) {
                    //获取注解
                    Component component = clazz.getAnnotation(Component.class);
                    String beanId = component.value();
                    //有这个注解的都要创建对象
                    Object bean = clazz.newInstance();
                    beanMap.put(beanId, bean);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        System.out.println(beanMap);
    }
}
```





### 声明Bean的注解

负责声明bean的注解，常见的包括四个：

* @Component：控制器，以下三个注解都是@Component的别名
* @Controller：组件，建议控制器类上使用
* @Service：业务，建议service类上使用
* @Repository：仓库，Dao，建议dao类上使用



#### spring注解的使用

在配置文件中添加context命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```



在配置文件中指定要扫描的包

**注意！！！   如果有多个包要扫描的话，在配置文件中指定多个包，用逗号隔开。或者指定多个包的共同父包(这种方法会损失一些效率)。**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.powernode.spring6.bean"/>
</beans>
```



在Bean类上使用注解

 **注意！！！  如果把value属性彻底去掉，spring会被Bean自动取名，并且默认名字的规律是：Bean类名首字母小写即可。**

```java
package com.powernode.spring6.bean;

import org.springframework.stereotype.Component;

@Component(value = "userBean")
public class User {
}

//如果写成这种方式，spring会被bean自动取名
@Component
public class User {
}
```











#### 选择性实例化Bean

```xml
	  <!--第一种解决方案：
              use-default-filters="false"，默认是true
              如果这个属性是false，表示com.xiehn.spring6.bean2包下所有的带有声明Bean的注解全部失效
       -->
       <context:component-scan base-package="com.xiehn.spring6.bean2" use-default-filters="false">
              <!--只有@Repository被包含进来，生效-->
              <context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
       </context:component-scan>
```



```xml
<!--第二种解决方案：
		context:exclude-filter表示排除掉哪些注解
-->
<context:component-scan base-package="com.powernode.spring6.bean3">
  <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
  <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
  <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```







### 负责注入的注解

Bean属性赋值需要用到这些注解：

* @Value
* @Autowired
* @Qualifier
* @Resource



#### @Value

当注入类型是简单类型时，可以使用value注解

@Value注解可以直接使用在属性上，也可以使用在setter方法上。都是可以的。都可以完成属性的赋值。
为了简化代码，一般不提供setter方法，直接在属性上使用@Value注解完成属性赋值。

```java
package com.powernode.spring6.bean4;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class User {

    private String name;

    private int age;

    public User(@Value("隔壁老王") String name, @Value("33") int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```





#### @Autowired与@Qualifier

@Autowired注解可以用来注入非简单类型。被翻译为：自动连线的，或者自动装配。
单独使用@Autowired注解，默认根据类型装配。【默认是byType】

该标注可以标注在哪里：

* 构造方法上
* 方法上
* 形参上
* 属性上
* 注解上

当有参数的构造方法只有一个时，@Autowired注解可以省略

@Autowired注解默认是byType进行注入的，也就是说根据类型注入的，如果一个接口中的程序有多个实现类，就会出现问题



**@Autowired注解和@Qualifier注解联合起来才可以根据名称进行装配，就可以解决一个接口不能用多个实现类的问题，在@Qualifier注解中指定Bean名称。**

```java
package com.powernode.spring6.dao;

import org.springframework.stereotype.Repository;

@Repository // 这里没有给bean起名，默认名字是：userDaoForOracle
public class UserDaoForOracle implements UserDao{
    @Override
    public void insert() {
        System.out.println("正在向Oracle数据库插入User数据");
    }
}
```



```java
package com.powernode.spring6.service;

import com.powernode.spring6.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private UserDao userDao;

    @Autowired
    @Qualifier("userDaoForOracle") // 这个是bean的名字。
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save(){
        userDao.insert();
    }
}
```









#### @Resource

@Resource注解也可以完成非简单类型注入。那它和@Autowired注解有什么区别？

* @Resource注解是JDK扩展包中的，也就是说属于JDK的一部分。所以该注解是标准注解，更加具有通用性。(JSR-250标准中制定的注解类型。JSR是Java规范提案。)
* @Autowired注解是Spring框架自己的。
* **@Resource注解默认根据名称装配byName，未指定name时，使用属性名作为name。通过name找不到的话会自动启动通过类型byType装配。**
* **@Autowired注解默认根据类型装配byType，如果想根据名称装配，需要配合@Qualifier注解一起用。**
* @Resource注解用在属性上、setter方法上。
* @Autowired注解用在属性上、setter方法上、构造方法上、构造方法参数上。



@Resource注解属于JDK扩展包，所以不在JDK当中，需要额外引入以下依赖：【如果是JDK8的话不需要额外引入依赖。高于JDK11或低于JDK8需要引入以下依赖。】

```xml
<dependency>
  <groupId>jakarta.annotation</groupId>
  <artifactId>jakarta.annotation-api</artifactId>
  <version>2.1.1</version>
</dependency>
```





**如果你用Spring6，要知道Spring6不再支持JavaEE，它支持的是JakartaEE9。（Oracle把JavaEE贡献给Apache了，Apache把JavaEE的名字改成JakartaEE了，大家之前所接触的所有的  javax.\*  包名统一修改为  jakarta.\*包名了。）**



![image-20230405011327753](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230405011327753.png)





**一句话总结@Resource注解：默认byName注入，没有指定name时把属性名当做name，根据name找不到时，才会byType注入。byType注入时，某种类型的Bean只能有一个。**







### 全注解式开发

所谓的全注解开发就是不再使用spring配置文件了。写一个配置类来代替配置文件。

```java
package com.powernode.spring6.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScans;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan({"com.powernode.spring6.dao", "com.powernode.spring6.service"})   //组件扫描
public class Spring6Configuration {
}
```



编写测试程序：不再new ClassPathXmlApplicationContext()对象了。

```java
@Test
public void testNoXml(){
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(Spring6Configuration.class);
    UserService userService = applicationContext.getBean("userService", UserService.class);
    userService.save();
}
```

