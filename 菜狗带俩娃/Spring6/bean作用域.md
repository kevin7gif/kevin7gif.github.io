Spring默认情况下是如何管理Bean的？

* **默认情况下Bean是单例的**

* 在Spring上下文初始化的时候实例化

  * ```java
    ApplicationContext applicationContext=new ClassPathXmlApplicationContext("spring-scope.xml");
    ```

* 每调用一次getBean()方法的时候，都返回那个单例的对象

  

  

scope属性的值：

* singleton：单例（默认）
* prototype：多例
* **下面这些值要求项目必须是一个web项目，当引入springmvc框架后，这些值就可以使用了**
* request：一个请求对应一个Bean。仅限于在WEB应用中使用。
* session：一个会话对应一个Bean。仅限于在WEB应用中使用。
* global session：portlet应用中专用的。如果在Servlet的WEB应用中使用global session的话，和session一个效果。（portlet和servlet都是规范。servlet运行在servlet容器中，例如Tomcat。portlet运行在portlet容器中。）
* application：一个应用对应一个Bean。仅限于在WEB应用中使用。
* websocket：一个websocket生命周期对应一个Bean。仅限于在WEB应用中使用。
* 自定义scope：很少使用。

当将Bean的scope属性设置为**prototype**：

```java
<bean class="com.xiehn.spring6.bean.SpringBean" id="springBean" scope="prototype"/>
```

* bean是多例的
* spring上下文初始化的时候就不会初始化这些bean
* 每一次调用getBean()方法的时候实例化这些bean