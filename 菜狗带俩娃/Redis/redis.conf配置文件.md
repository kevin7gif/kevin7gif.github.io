### 配置文件详解

配置文件 unit单位 对大小写不敏感

![image-20230507211056890](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230507211056890.png)

网络

```
# bind 127.0.0.1 -::1         绑定的ip
protected-mode yes            保护模式
port 6379                     端口设置
requirepass xhn021019         密码设置
```

开启远程连接时需要将bind那一栏注释掉，表示允许所有ip进行访问

daemonize设置为yes,表示开启守护进程

requirepass那一栏将注释去掉，表示需要密码进行登录



通用

```
daemonize yes   表示以守护进程的方式运行，默认是no
pidfile /var/run/redis_6379.pid   如果以后台的方式运行，就需要指定一个pid文件
databases 16   默认的数据库数量，16个

#debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level) 调试和测试环境
# notice (moderately verbose, what you want in production probably)   生产环境
# warning (only very important / critical messages are logged)
loglevel notice      设置日志信息的级别

快照，持久化：在规定的时间内，执行了多少次操作，则会持久化到文件
redis是内存数据库，如果没有持久化，那么数据断电即失
# save 3600 1    如果3600s内，至少有一个key进行了修改，则会进行持久化操作
# save 300 100
# save 60 10000

stop-writes-on-bgsave-error yes    持久化如果出错，是否还要继续工作，默认为yes 
rdbcompression yes                 是否压缩rdb文件，默认为yes，需要消耗一些cpu资源
rdbchecksum yes                    保存rdb文件时，进行错误检验
dir ./                             rdb文件保存的目录

# maxclients 10000                 设置能连接上redis的最大客户端数量
 maxmemory-policy noeviction       内存达到上限的处理策略

```

