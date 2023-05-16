>### docker安装nginx



```shell
1.搜索镜像	docker search nginx
2.下载镜像	docker pull nginx
3.运行测试
[root@master KG]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   16 months ago   141MB
mysql        5.7       c20987f18b13   16 months ago   448MB

# -d 后台运行	--name 给容器命名	-p 宿主机端口：容器内部端口
[root@master KG]# docker run -d --name nginx01 -p 3344:80 nginx
a1671b968231b7d72d0749bd3dc499c13136363316b33c52557e375cf6f117ec

[root@master KG]# docker ps
CONTAINER ID   IMAGE     COMMAND                   CREATED         STATUS         PORTS                                   NAMES
a1671b968231   nginx     "/docker-entrypoint.…"   4 seconds ago   Up 3 seconds   0.0.0.0:3344->80/tcp, :::3344->80/tcp   nginx01

使用curl可以进行测试，效果等同于在宿主机浏览器地址栏上输入：虚拟机ip:3344
[root@master KG]# curl localhost:3344

4.进入容器
[root@master etc]# docker exec -it nginx01 /bin/bash
root@a1671b968231:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@a1671b968231:/# cd /etc/nginx/
root@a1671b968231:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params

```

**思考：每次启动nginx配置文件，都需要进入容器内部，十分麻烦，能否在容器外部提供一个映射路径，达到在容器外修改文件名，容器内部就可以自动修改？  -v 数据卷**





>### docker安装tomcat



```shell
1.搜索镜像 docker search tomcat
2.下载镜像 docker pull tomcat:10.0
3.运行测试
[root@master etc]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   16 months ago   141MB
tomcat       10.0      fb5657adc892   16 months ago   680MB
mysql        5.7       c20987f18b13   16 months ago   448MB

# 启动运行
[root@master etc]# docker run -d -p 3355:8080 --name tomcato1 tomcat

[root@master etc]# docker ps
CONTAINER ID   IMAGE     COMMAND             CREATED          STATUS          PORTS                                       NAMES
25f8c8d07ebf   tomcat    "catalina.sh run"   18 seconds ago   Up 17 seconds   0.0.0.0:3355->8080/tcp, :::3355->8080/tcp   tomcato1

测试访问没有问题

4.进入容器
[root@master etc]# docker exec -it tomcato1 /bin/bash

# 发现问题：1.linux命令少了 2.没有webapps：阿里云镜像的原因，默认是最小的镜像，所有不必要的都剔除掉了
# 保证最小的可运行环境
```

**思考：每次启动tomcat服务都需要进入容器内部，十分麻烦，要是可以在容器外部提供一个映射路径，webapps，我们在外部部署项目，就自动同步到内部就好了**







>### docker中安装mysql



```shell
1.搜索镜像 docker search mysql
2.下载镜像 docker pull mysql:5.7
3.运行测试
[root@master etc]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   16 months ago   141MB
tomcat       10.0      fb5657adc892   16 months ago   680MB
mysql        5.7       c20987f18b13   16 months ago   448MB

# 运行容器，需要做数据挂载！  # 安装启动mysql，需要配置密码

-d 后台运行
-p 端口映射--- 宿主机端口：容器内端口
-v 数据卷挂载
-e 环境配置--- 配置mysql数据库密码 
--name 容器名字
[root@master etc]# docker run -d -p 3355:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=12456 --name mysql001 mysql:5.7

# 启动成功之后，使用mysql连接工具进行测试
```



![image-20230515003346232](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230515003346232.png)
