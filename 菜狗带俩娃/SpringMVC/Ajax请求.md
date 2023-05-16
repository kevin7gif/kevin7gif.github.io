- **AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。**
- AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
- **Ajax 不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术。**



完成ajax请求访问服务器，返回学生集合

* 添加jackson依赖
* 在webapp目录下新建js目录，添加jQuery函数库
* 在index.jsp上导入函数库
* 在controller的方法上添加注解@ResponseBody，专门用来处理ajax请求
* 在springmvc.xml文件中添加注解驱动<mvc:annotation-driven>，它用来解析@ResponseBody注解



### <mvc:annotation-driven>标签的使用

会自动注册两个bean，分别为：

* **DedaultAnnotationHandlerMapping**
* **AnnotationMethodHandlerAdapter**
* 是springmvc为@controller分发请求所必需的



除了注册这两个bean，还提供了很多支持

* 支持使用ConversionService实例对表单参数进行类型转换
* 支持使用@NumberFormat、@DateTimeFormat
* 注解完成数据类型的格式化
* 支持使用@RequestBody和@ResponseBody注解
* 静态资源的分流也使用这个标签







### 资源在WEB-INF目录下

此目录下的动态资源，只能通过请求转发的方式进行访问