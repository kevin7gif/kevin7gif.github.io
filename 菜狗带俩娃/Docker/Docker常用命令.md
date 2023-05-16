![image-20230514003600175](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514003600175.png)







### 帮助命令

```shell
docker version          #显示docker的版本信息
docker info             #显示docker的系统信息（包括镜像和容器的数量）
docker 命令 --help      #帮助命令
```

帮助文档的地址：https://docs.docker.com/reference/



### 镜像和容器的关系

* **镜像相当于一个安装包，容器就是已安装的app**

* **镜像可以看作是一个java类，而容器就可以看作是该类的实例化对象；一个镜像可以创建N个容器，每个处于运行状态的容器中都包含着一个或多个相关的应用，且它的运行不会干扰到其他容器，它们之间相互隔离**

* **docker的整个生命周期有三部分组成：镜像（image）+容器（container）+仓库（repository)**
* **docker容器=镜像+可读层**
  * ![img](https://img-blog.csdnimg.cn/dd7882ef264643c18a5378dff41b04f4.png)
* **容器是由镜像实例化而来简单来说，镜像是文件，容器是进程,容器是基于镜像创建的，即容器中的进程依赖于镜像中的文件。**
* **docker 的镜像概念类似虚拟机的镜像。是一个只读的模板，一个独立的文件系统，包括运行容器所需的数据，可以用来创建新的容器。docker利用容器来运行应用：docker容器是由docker镜像创建的运行实例。**
  * **一个镜像可以包含一个完整的ubuntu操作系统环境，里面仅安装了mysql或用户需要的其他应用程序。**





### 镜像命令

==docker images== 查看所有本地主机上的镜像

```shell
[root@master docker]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED      SIZE
hello-world   latest    9c7a54a9a43c   8 days ago   13.3kB

# 解释
REPOSITORY		镜像的仓库源
TAG				镜像的标签
IMAGE ID		镜像的id
CREATED			镜像的创建时间
SIZE			镜像的大小

# 可选项
	-a，--all		列出所有的镜像
	-q，--quiet		只显示镜像的id
```



==docker search== 搜索镜像

```shell
[root@master docker]# docker search mysql
NAME                            DESCRIPTION                                      STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   14122     [OK]       
mariadb                         MariaDB Server is a high performing open sou…   5394      [OK]       
percona                         Percona Server is a fork of the MySQL relati…   607       [OK]       
phpmyadmin                      phpMyAdmin - A web interface for MySQL and M…   793       [OK]       
circleci/mysql                  MySQL is a widely used, open-source relation…   29                   
bitnami/mysql                   Bitnami MySQL Docker Image                      85                   [OK]

```



==docker pull== 下载镜像

```shell
[root@master docker]# docker pull mysql:5.7
5.7: Pulling from library/mysql
72a69066d2fe: Pull complete			分层下载，docker image的核心  联合文件系统      
93619dbc5b36: Pull complete 
99da31dd6142: Pull complete 
626033c43d70: Pull complete 
37d5d7efb64e: Pull complete 
ac563158d721: Pull complete 
d2ba16033dad: Pull complete 
0ceb82207cd7: Pull complete 
37f2405cae96: Pull complete 
e2482e017e53: Pull complete 
70deed891d42: Pull complete 
Digest: sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94		签名，防伪标志
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7			真实地址


# 等价于它
docker pull mysql:5.7
docker pull docker.io/library/mysql:5.7	
```



==docker rmi== 删除镜像

```shell
[root@master docker]# docker rmi -f 9c7a54a9a43c
Untagged: hello-world:latest
Untagged: hello-world@sha256:fc6cf906cbfa013e80938cdf0bb199fbdbb86d6e3e013783e5a766f50f5dbce0
Deleted: sha256:9c7a54a9a43cca047013b82af109fe963fde787f63f9e016fdc3384500c2823d
```

```shell
[root@master docker]# docker rmi -f 容器id				   删除指定的镜像
[root@master docker]# docker rmi -f 容器id 容器id 容器id		删除多个镜像
[root@master docker]# docker rmi -f $(docker images -aq)	删除全部镜像
```







### 容器命令



==有了镜像才可以创建容器，可以下载一个centos镜像进行测试学习==

```shell
docker pull centos
```



==新建容器并启动==

```shell
docker run [可选参数] image

# 参数说明
--name="Name"    容器名字，例如：tomcat01，tomcat02，用来区分容器
-d               以后台方式运行
-it              使用交互的方式运行，进入容器查看内容
-p               指定容器的端口
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口 （常用）
	-p 容器端口
	容器端口
-P               随机指定端口
```



==列出所有的运行的容器==

```shell
# docker ps 命令
	 # 列出当前正在运行的容器
-a	 # 列出当前正在运行的容器+带出历史运行过的容器
-n=? # 显示最近创建的容器
-q   # 只显示容器的编号

[root@master docker]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@master docker]# docker ps -a
CONTAINER ID   IMAGE          COMMAND    CREATED       STATUS                   PORTS     NAMES
4767ec710858   9c7a54a9a43c   "/hello"   3 hours ago   Exited (0) 3 hours ago             vigilant_bhabha

```



==退出容器==

```shell
 exit         # 直接让容器停止并退出
 Ctrl+P+Q     # 容器不停止并退出
```



==删除容器==

```shell
docker rm 容器id                   # 删除指定的容器，不能删除正在运行的容器，如果要强制删除：rm -f
docker rm -f $(docker ps -aq)     # 删除所有的容器
docker ps -a -q|xargs docker rm   # 删除所有的容器
```



==启动和停止容器==

```shell
docker start 容器id      # 启动容器
docker restart 容器id    # 重启容器
docker stop 容器id       # 停止当前正在运行的容器
docker kill 容器id       # 强制停止当前容器
```



==在后台通过镜像创建容器并运行==

```shell
# 命令	docker run -d 镜像
例如：
[root@master docker]# docker run -d centos

# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# 例如：nginx容器启动后，发现自己没有提供服务，就会立即停止，就是没有程序了
```



==查看日志==

```shell
docker logs -f -t --tail 日志条数 容器id
-t                # 时间戳
-f                # 实时打印
--tail number     # 用number指定日志条数
```



==查看容器中进程信息==

```shell
[root@master docker]# docker top 容器id
```



==查看镜像的元数据==

```shell
[root@master docker]# docker inspect 容器id
```



==进入当前正在运行的容器==

```shell
# 通常容器都是使用后台方式运行

# 命令
docker exec -it 容器id bashShell（bin/bash）

docker attach 容器id

# docker exec      # 进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach    # 进入容器正在执行的终端，不会启动新的进程！
```



==从容器内拷贝文件到主机上==

```shell
docker cp 容器id：容器内路径 目的主机路径
```

