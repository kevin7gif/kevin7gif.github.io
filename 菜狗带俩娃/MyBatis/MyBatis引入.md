Java常用框架

* SSM三大框架：Spring+SpringMVC+Mybatis
* SpringBoot
* SpringCloud

框架其实就是对通用代码的封装，提前写好了一堆接口和类，我们可以在做项目的时候直接引入这些接口和类（引入框架），基于这些现有的类和框架进行开发，可以大大提高开发效率

框架一般都是以jar包的形式存在。（jar包中有class文件以及各种配置文件等）



JDBC的不足：

* SQL语句写死在Java程序中，不灵活，改SQL的话就要改Java代码，违背开闭原则OCP
* 给？传值是繁琐的，不能自动化
* 将查询结果集封装成Java对象是繁琐的，不能自动化



ORM：对象、关系、映射

* O（Object）：JVM中的Java对象
* R（Relational）：关系型数据库
* M（Mapping）：映射

![001-ORM思想-对象关系映射.png](https://cdn.nlark.com/yuque/0/2023/png/27467446/1675044994253-6fe6d43c-acb4-4d1d-9ea1-44063a8f208d.png)