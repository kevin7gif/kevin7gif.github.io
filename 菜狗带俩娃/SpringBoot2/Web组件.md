1 . 为什么要使用 Spring Boot 

因为Spring， SpringMVC 需要使用的大量的配置文件 （xml文件） 还需要配置各种对象，把使用的对象放入到spring容器中才能使用对象 需要了解其他框架配置规则。 

2 . SpringBoot 就相当于 不需要配置文件的Spring+SpringMVC。 常用的框架和第三方库都已经配置好了。 拿来就可以使用了。 

3 . SpringBoot开发效率高，使用方便多了 



以下都是进行纯注解式开发，意思就是不用在xml文件中进行配置了

### JavaConfig

JavaConfig: 使用java类作为xml配置文件的替代， 是配置spring容器的纯java的方式。 在这个java类这可以创建java对象，把对象放入spring容器中（注入到容器），

使用两个注解：
1）@Configuration  ： 放在一个类的上面，表示这个类是作为配置文件使用的。 
2）@Bean：声明对象，把对象注入到容器中。

```java
例子：
package com.bjpowernode.config;
​
import com.bjpowernode.vo.Student;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
​
/**
 * Configuration:表示当前类是作为配置文件使用的。 就是用来配置容器的
 *       位置：在类的上面
 *
 *  SpringConfig这个类就相当于beans.xml
 */
@Configuration
public class SpringConfig {
    /**
     * 创建方法，方法的返回值是对象。 在方法的上面加入@Bean
     * 方法的返回值对象就注入到容器中。
     *
     * @Bean: 把对象注入到spring容器中。 作用相当于<bean>
     *        位置：方法的上面
     *        说明：@Bean,不指定对象的名称，默认是方法名是 id
     */
    @Bean
    public Student createStudent(){
        Student s1  = new Student();
        s1.setName("张三");
        s1.setAge(26);
        s1.setSex("男");
        return s1;
    }
​
    /***
     * 指定对象在容器中的名称（指定<bean>的id属性）
     * @Bean的name属性，指定对象的名称（id）
     */
    @Bean(name = "lisiStudent")
    public Student makeStudent(){
        Student s2  = new Student();
        s2.setName("李四");
        s2.setAge(22);
        s2.setSex("男");
        return s2;
    }
}
```





### @ImporResource

作用：导入其他的xml配置文件

```xml
<!--等于在xml文件中进行如下配置-->
<import resources="其他配置文件"/>
```

使用注解的方式：

```java
@Configuration
@ImportResource(value ={ "classpath:applicationContext.xml","classpath:beans.xml"})
public class SpringConfig {
}
```





### @PropertyResource

作用：读取properties属性配置文件。 使用属性配置文件可以实现外部化配置 ，在程序代码之外提供数据。

步骤：

- 在resource目录下，创建properties文件
- 在PropertyResource中指定properties文件的位置
- 使用@Value(value = "${key}")

```java
@Configuration
@ImportResource(value ={ "classpath:applicationContext.xml","classpath:beans.xml"})
@PropertySource(value = "classpath:config.properties")
@ComponentScan(basePackages = "com.bjpowernode.vo") //开启组件扫描
public class SpringConfig {
}
```





------

### SpringBoot入门

可以简化Spring，SpringMVC的使用，**核心还是IOC容器**

特点：

- 创建sring应用
- 内嵌tomcat，jetty，Undertow等服务器
- 提供了starter起步依赖，简化应用配置
  - 比如使用Mybatis框架，需要在Spring项目中，配置MyBatis的对象 SqlSessionFactory ， Dao的代理对象
  - 在SpringBoot项目中，在pom.xml里面, 加入一个 mybatis-spring-boot-starter依赖 
- 尽可能去配置spring和第三方库。叫做自动配置（就是把spring中的，第三方库中的对象都创建好，放到容器中， 开发人员可以直接使用）
- 提供了健康检查， 统计，外部化配置 
- 不用生成代码， 不用使用xml配置文件 





第一种创建方式：使用springboot提供的初始化器

使用的地址： [https://start.spring.io](https://start.spring.io/)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1534487/1650267938232-0480ab2f-507d-44c3-851d-89e7a8013448.png)



第二种方式：使用国内的地址：

[https://start.springboot.io](https://start.springboot.io/)



@ResponseBody：**将返回值当作数据来用**，不经过视图解析器，直接将数据写入到输入流，通常用来返回json数据或者xml数据





### 注解的使用

#### @SpringBootApplication --------》复合注解

- @SpringBootConfiguration

  - ```java
    //@SpringBootConfiguration
        
    @Configuration
    public @interface SpringBootConfiguration {
        @AliasFor(
            annotation = Configuration.class
        )
        boolean proxyBeanMethods() default true;
    }
    ​
    //说明：使用了@SpringBootConfiguration注解标注的类，可以作为配置文件使用的，
    //可以使用Bean声明对象，注入到容器
    ```

  - 

- @EnableAutoConfiguration

  - ```java
    // 启用自动配置， 把java对象配置好，注入到spring容器中。例如可以把mybatis的对象创建好，放入到容器中。
    ```

- @ComponentScan

  ```{0} ```java
         //@ComponentScan 扫描器，找到注解，根据注解的功能创建对象，给属性赋值等等。
         //默认扫描的包： @ComponentScan所在的类所在的包和子包。
         ```



```@Value

读取配置文件中的key值

```properties
#配置端口号
server.port=8082
#context-path
server.servlet.context-path=/myboot
​
#自定义key=value
school.name=动力节点
school.website=www.bjpowernode.com
school.address=北京的大兴区
​
site=www.bjpowernode.com
```

编写controller类

```java
@Controller
public class HelloSpringBoot {
    @Value("${server.port}")
    private Integer port;
    @Value("${server.servlet.context-path}")
    private String contextPath;
    @Value("${school.name}")
    private String name;
    @Value("${site}")
    private String site;
    @Value("${school.address}")
    private String address;
    @RequestMapping("/data")
    @ResponseBody
    public String helloSpringBoot(){
        return name+",site="+site+",项目的访问地址是："+contextPath+",使用的端口号是："+port+",该地点在："+address;
    }
}
```







### SpringBoot的配置文件

配置文件名称： application

扩展名有： properties( k=v) ;  yml ( k: v)

使用application.properties,  application.yml（**若二者同时存在时，application.properties优先级更高！**）

例1：application.properties设置 端口和上下文

```properties
#设置端口号
server.port=8082
#设置访问应用上下文路径， contextpath
server.servlet.context-path=/myboot
```

例2： application.yml

```yml
server:
  port: 8083
  servlet:
    context-path: /myboot2
```





```多环境配置

有开发环境， 测试环境， 上线的环境。

每个环境有不同的配置信息， 例如端口， 上下文件， 数据库url，用户名，密码等等

使用多环境配置文件，可以方便的切换不同的配置。

使用方式： 创建多个配置文件， 名称规则： application-环境名称.properties(yml)

创建开发环境的配置文件： application-dev.properties(  application-dev.yml )

创建测试者使用的配置： application-test.properties





### @ConfigurationProperties

作用： 把配置文件的数据映射为java对象。

属性：prefix 配置文件中的某些key的开头的内容

* 
#配置端口号
server.port=8082
#context-path
server.servlet.context-path=/myboot

#自定义key=value
school.name=动力节点
school.website=www.bjpowernode.com
school.address=北京的大兴区

site=www.bjpowernode.com
* 

```java
@Component
@ConfigurationProperties(prefix = "school")
public class SchoolInfo {

    private String name;

    private String website;

    private String address;


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getWebsite() {
        return website;
    }

    public void setWebsite(String website) {
        this.website = website;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "SchoolInfo{" +
                "name='" + name + '\'' +
                ", website='" + website + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

```java
 	@Resource
    private SchoolInfo info; 

	@RequestMapping("/info")
    @ResponseBody
    public String info(){
        return "SchoolInfo对象----》"+info.toString();
    }
```







### 使用Jsp

springboot不推荐使用jsp，而是使用模板技术代替jsp

步骤：

加入一个处理jsp的依赖。 负责编译jsp文件

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
```

如果需要使用servlet， jsp，jstl的功能

```xml
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>

<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
</dependency>

<dependency>
<groupId>javax.servlet.jsp</groupId>
	<artifactId>javax.servlet.jsp-api</artifactId>
	<version>2.3.1</version>
</dependency>
```



创建一个存放jsp的目录，一般叫做webapp

   index.jsp

需要在pom.xml指定jsp文件编译后的存放目录。

```xml
<!--指定jsp编译后的存放目录-->
        <resources>
            <resource>
                <!--jsp原来的目录-->
                <directory>src/main/webapp</directory>
                <!--指定编译后的存放目录-->
                <targetPath>META-INF/resources</targetPath>
                <!--指定处理的目录和文件-->
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
        </resources>
```

创建controller，访问jsp

```java
@Controller
public class JspController {
    @RequestMapping("/myjsp")
    public String daJsp(HttpServletRequest request){
        request.setAttribute("data","springboot使用了jsp！");
        return "index";
    }
}
```

在application.propertis文件中配置视图解析器

```properties
#配置视图解析器
#  “/ ”：src/main/webapp
spring.mvc.view.prefix=/
spring.mvc.view.suffix=.jsp
```

