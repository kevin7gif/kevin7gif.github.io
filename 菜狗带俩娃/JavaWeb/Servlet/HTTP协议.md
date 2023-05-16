HTTP协议

目前为止接触过的缓存机制

* 堆内存中的字符串常量池
  * “abc”先在字符串常量池中查找，如果有直接用，如果没有先创建再放入字符串常量池
* 堆内存当中的整数型常量池
  * [-128~127]一个256个Integer类型的引用，放在整数型常量池中，如果没有超出这个范围直接从常量池中取
* 连接池（Connection Cache）
  * 这里所说的连接是java语言数据库的连接对象：java.sql.Connection对象
  * JVM是一个进程，MySQL是一个进程，进程和进程之间按连接打开通道是很耗费资源的。可以提前先创建好N个Connection连接对象，将连接对象放到一个集合当中，我们把这个放有Connection连接对象的集合称为连接池，每一次用户连接的时候不需要再新建连接对象，省去了新建的环节，直接从连接池中获取连接对象，大大提升访问效率
* 线程池
  * Tomcat服务器本身就是支持多线程的
  * Tomcat服务器启动的时候会先创建好N多个线程对象，然后将线程对象放到集合中，称为线程池。用户发送请求过来后，需要有一个对应的线程来处理该请求，这个时候线程对象就会直接从线程池中拿，效率高
  * 所有的WEB服务器或者应用服务器都是支持多线程的，都有线程池机制
* redis
  * NoSQL数据库，非关系型数据库，缓存数据库
* 向ServletContext应用域中存储数据，也等于将数据放到缓存cache中

---

HTYP的请求协议（B---->S）

* HTTP请求协议包括4部分
  * 请求行
  * 请求头
  * 空白行
  * 请求体

HTTP请求协议的具体报文：get请求

```
//请求行
GET /Servlet3/getServlet?username=xhn&userpwd=123456 HTTP/1.1
//请求头
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Connection: keep-alive
Cookie: Idea-aafc917b=cc94ca87-29b8-4788-ae2e-af89d14bb21e
Host: localhost:8080
Referer: http://localhost:8080/Servlet3/index.html
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36
sec-ch-ua: "Chromium";v="104", " Not A;Brand";v="99", "Google Chrome";v="104"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
//空白行

//请求体

```



HTTP请求协议的具体报文：post请求

```
//请求行
POST /Servlet3/postServlet HTTP/1.1
//请求头
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: max-age=0
Connection: keep-alive
Content-Length: 25
Content-Type: application/x-www-form-urlencoded
Cookie: Idea-aafc917b=cc94ca87-29b8-4788-ae2e-af89d14bb21e
Host: localhost:8080
Origin: http://localhost:8080
Referer: http://localhost:8080/Servlet3/index.html
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36
sec-ch-ua: "Chromium";v="104", " Not A;Brand";v="99", "Google Chrome";v="104"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
//空白行

//请求体

```

* 请求行

  * 第一部分：请求方式
    * get
    * post
    * delete
    * put
    * head
    * options
    * trace
  * 第二部分：URI
    * 统一资源标识符，代表网络中某个资源的名字
    * URL:统一资源定位符，代表网络中某个资源，同时，URL可以定位到该资源
      * URL包括URI
      * 例如：http://localhost/servlet1/index.html是URL，/servlet1/index.html是URI
  * 第三部分：HTTP协议版本号

* 请求头

  * 请求的主机
  * 主机的端口
  * 浏览器信息
  * 平台信息
  * cookie等信息

* 空白行

  * 区分请求头和请求体

* 请求体

  * 向服务器发送的具体数据

    

* HTTP响应协议（S---->B）
  * 状态行
  * 响应头
  * 空白行
  * 响应体

HTTP响应协议的具体报文

```
//状态行
HTTP/1.1 200 ok      
//响应头
Content-Type: text/html;charset=UTF-8
Content-Length: 152
Date: Tue, 21 Feb 2023 12:48:37 GMT
Keep-Alive: timeout=20
Connection: keep-alive
//空白行

//响应体
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>from get servlet</title>
</head>
<body>
    <h1>from get servlet</h1>
</body>
</html>
```

* 状态行

  * 三部分组成
    * 第一部分：协议版本号（HTTP/1.1）
    * 第二部分：状态码（HTTP中规定的响应状态号，不同的响应结果对应不同的号码）
      * 200表示请求响应成功，正常结束
      * 404表示访问的资源不存在，属于前端错误，要么是路径写错了，要么是路径写对了，但是服务器中对应的资源并没有启动成功
      * 405表示前端发生的请求方式与后端请求的处理方式不一致
        * 前端是post请求，后端是get请求。反之也是
      * 500表示服务器端程序出现异常
      * 以4开头，一般是浏览器的错误导致
      * 以5开头一般是服务器的错误导致
    * 第三部分：状态的描述信息
      * ok表示正常结束
      * not found表示资源无法找到

* 响应头

  * 响应的内容类型
  * 响应的内容长度
  * 响应的时间
  * ......

* 空白行

  * 用来分隔响应头和响应体

* 响应体

  * 响应的正文，这些内容是一个长的字符串，这个字符串被浏览器渲染，解释并执行，最终展示出效果

* 怎么查看协议内容？

  * 按F12，找到network，通过这个面板可以查看协议的具体内容

    

---

使用form表单并且form表单中的method属性值为：post，这种情况下可以发送post请求

其他情况都是get请求



get请求和post请求的区别？

* get请求发送数据的时候，数据会挂在URI的后面，并且在URI的后面加上一个"？"，后面是数据，这样会导致发送的数据回显到浏览器地址栏上（get请求在请求行上发送数据）
* post请求发送数据时，在请求体中发送，不会回显到浏览器的地址栏上
* get请求只能发送普通的字符串，并且发送的字符串长度有限制
* get请求无法发送大数据量
* post请求可以发送任意类型的数据：普通字符串，流媒体等信息，视频，声音，图片，可以发送大数据量
* get请求比较适合从服务器端获取数据，post请求比较适合向服务器端发送数据
* get请求是绝对安全的，因为get请求只是从服务器端获取数据，post请求是向服务器提交数据 
* get请求支持缓存，任何一个get请求最终的“响应结束”都会被浏览器缓存起来，在浏览器缓存当中，一个get请求的路径对应一个资源
* 实际上，你只要发送了get请求，浏览器做的第一件事就是先从本地浏览器缓存中找，找不到的时候才会去服务器上获取，这种缓存机制目的是为了提高用户体验
* post请求不支持缓存，post请求之后服务器“响应的结果”不会被浏览器缓存起来，因为这个缓存没有意义
* 如果不想让get请求在本地浏览器缓存上查找的话，可以在路径的后面添加一个时间戳，这样每次请求的路径都不一样，浏览器也就不走缓存了

不管是post请求还是get请求，发送的数据格式都是一样的，只不过位置不同

* name=value&name=value&name=value
* 以form表单为例：
  * form表单中input标签的name就是上述的name
  * form表单中input标签的value就是上述的value



---

### 模板方法设计模式

* 在模板类的模板方法中定义核心算法骨架，具体的实现步骤可以延迟到子类中完成
* 模板类通常是一个抽象类，模板类当中的模板方法是核心算法，这个方法通常是final（也可以不是final）
* 模板类当中的抽象方法就是不确定实现的方法，具体怎么实现交给子类去做

设计模式

* 解决某个问题的固定解决方案（可以被重复使用）

常见的设计模式

* GoF设计模式
  * 单例模式
  * 工厂模式
  * 代理模式
  * 门面模式
  * 责任链模式
  * 观察者模式
  * 模板方法设计模式
  * ......
* javaEE设计模式
* ......