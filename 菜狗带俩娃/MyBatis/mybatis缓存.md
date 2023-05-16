![007-对缓存的理解.png](https://cdn.nlark.com/yuque/0/2023/png/27467446/1676341928730-3e821ced-b3a4-4d8d-a4b7-cae0ff98227a.png)



mybatis的缓存：将select语句的查询结果放到缓存（内存）当中，下一次还是这条select语句的话，直接从缓存中取，不再查数据库。一方面是减少了IO。另一方面不再执行繁琐的查找算法。效率大大提升。

#### mybatis缓存包括：

* 一级缓存：将查询到的数据存储到SqlSession中。
  - 默认是开启的，不需要任何配置
  - 原理：只要使用同一个SqlSession对象执行同一条SQL语句，就会走缓存
* 二级缓存：将查询到的数据存储到SqlSessionFactory中。
* 或者集成其它第三方的缓存：比如EhCache【Java语言开发的】、Memcache【C语言开发的】等。
  缓存只针对于DQL语句，也就是说缓存机制只对应select语句。



什么情况下不走缓存？

* 第一种：不同的SqlSession对象。

* 第二种：查询条件变化了。

  

  



一级缓存失效情况包括两种：

第一种：第一次查询和第二次查询之间，手动清空了一级缓存。

```
sqlSession.clearCache();
```

第二种：第一次查询和第二次查询之间，执行了增删改操作。【这个增删改和哪张表没有关系，只要有insert delete update操作，一级缓存就失效。】





二级缓存的范围是SqlSessionFactory。

使用二级缓存需要具备以下几个条件：

1.<setting name="cacheEnabled" value="true"> 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。默认就是true，无需设置。
2.在需要使用二级缓存的SqlMapper.xml文件中添加配置：<cache />
3.使用二级缓存的实体类对象必须是可序列化的，也就是必须实现java.io.Serializable接口
4.SqlSession对象关闭或提交之后，一级缓存中的数据才会被写入到二级缓存当中。此时二级缓存才可用。



二级缓存的失效：只要两次查询之间出现了增删改操作。二级缓存就会失效。【一级缓存也会失效】
