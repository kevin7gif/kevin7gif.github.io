NoSQL（非关系型数据库）在以下几种情况比较适用：

* 数据模型比较简单
* 需要灵活性更强的IT系统
* 对数据库性能要求较高
* 不需要高度的数据一致性
* 对于给定的key，比较容易映射复杂值的环境



Redis是一个开源的，**高性能键值对数据库**，也就是一个非关系型数据库，可以用作数据库、缓存和消息中间件

**Redis是一个简单的，高效的，分布式的，基于内存的缓存工具**



### Redis特点

* **性能极高** 
  * 由于数据是存储在内存中
* **丰富的数据类型**
  * string，Hash，List，Set，Ordered Set等
* **原子性**
  * 要么成功执行，要么失败全不执行。单个操作是原子性的，多个操作也支持事务，通过MULTI和EXEC指令包起来
* **丰富的特性**
  * 支持publich / subscribe，通知，key过期等特性
* **高速读写**
  * 使用自己实现的分离器，代码量很短，没有使用lock(MySQL)，效率很高



**windows版本的redis**

redis-server.exe：服务端， 启动后，不要关闭

redis-cli.exe：客户端， 访问redis中的数据



RedisTemplate 使用的是 lettuce 客户端库

```xml
<!--redis起步依赖： 直接在项目中使用RedisTemplate(StringRedisTemplate)-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
data-redis使用的   lettuce 客户端库

在程序中使用RedisTemplate类的方法 操作redis数据， 实际就是调用的lettuce 客户端的中的方法
```



例如：

在springboot框架中添加如下依赖：

```xml
 <!--redis起步依赖：直接在项目中使用RedisTemplate(StringRedisTemplate)-->
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

在application.properties配置文件中进行如下配置：

```properties
#配置服务器端口和访问应用上下文路径
server.port=8082
server.servlet.context-path=/myRedis

#指定redis
spring.redis.port=6379
spring.redis.host=localhost
spring.redis.password=xhn021019
```

编写一个controller类

```java
@RestController
public class RedisController {

    /**
     * 注入RedisTemplate
     *
     * RedisTemplate泛型
     * RedisTemplate<String,String>
     * RedisTemplate<Object,Object>
     * RedisTemplate
     */
    @Resource
    private RedisTemplate redisTemplate;

    //添加数据到redis
    @PostMapping("/addstring")
    public String addToRedis(String name,String value){

        //操作Redis中string类型的数据，先获取ValueOperation对象
        ValueOperations valueOperations=redisTemplate.opsForValue();
        //添加数据到redis
        valueOperations.set("myname","张三");

        return "向redis添加string类型的数据";
    }

    //从redis中获取数据
    @GetMapping("/getKey")
    public String getData(String key){
        ValueOperations valueOperations=redisTemplate.opsForValue();
        Object o = valueOperations.get(key);
        return "key是："+key+"，它的值是："+o;
    }
}
```

在postman中进行测试





### 对比 StringRedisTemplate 和 RedisTemplate

StringRedisTemplate ： 把k，v 都是作为String处理， 使用的是String的序列化 ， 可读性好

RedisTemplate ： 把k，v 经过了序列化存到redis。 k，v 是序列化的内容， 不能直接识别.

* 默认使用的jdk序列化， 可以修改为前提的序列化



序列化：把对象转化为可传输的字节序列过程称为序列化。

反序列化：把字节序列还原为对象的过程称为反序列化。



#### 为什么进行序列化？

序列化最终的目的是**为了对象可以跨平台存储，和进行网络传输**。

而我们进行**跨平台存储和网络传输的方式就是IO**，而我们的IO支持的数据格式就是**字节数组**。

我们必须在把对象转成字节数组的时候就制定一种规则（序列化）

那么我们从IO流里面读出数据的时候再以这种规则把对象还原回来（反序列化）。



#### 序列化的方式

JDK（不支持跨语言）、JSON、XML、Hessian、Kryo（不支持跨语言）、Thrift、Protofbuff

Student( name=zs, age=20)  ----  { "name":"zs", "age":20 }

java的序列化： 把java对象**转为byte[ ], 二进制数据**

json序列化：json序列化功能将对象**转换为 JSON 格式**或从 JSON 格式转换对象。

例如：把一个Student对象转换为JSON字符串 {"name":"李四", "age":29} ，反序列化(将JSON字符串 {"name":"李四", "age":29} 转换为Student对象





设置key或value的序列化方式

```java
// 使用RedisTemplate ，在存取值之前，设置序列化
// 设置 key 使用String的序列化
redisTemplate.setKeySerializer( new StringRedisSerializer());

// 设置 value 的序列化
redisTemplate.setValueSerializer( new StringRedisSerializer());

redisTemplate.opsForValue().set(k,v);
```

