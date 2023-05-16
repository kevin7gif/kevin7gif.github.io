### Docker系统架构

![image-20230514004757347](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514004757347.png)







### Docker作用



**docker和虚拟机的不同：**

* 传统虚拟机，虚拟出一条硬件，运行一个完整的操作系统，然后在系统上安装和运行软件，存在独立的硬件系统和操作系统
* 容器内的应用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟我们的硬件，共享的宿主机中的操作系统和硬件系统，所以更轻便
* 每个容器是互相隔离的，每个容器内都有一个属于自己的文件系统，互不影响



**虚拟机缺点：**

1.资源占用多

2.冗余步骤多

3.启动很慢



**容器化技术：**

==容器化技术不是模拟的一个完整的操作系统==

**应用更快速的交付和部署**

​	传统：一堆帮助文档，安装程序

​	docker：打包镜像发布测试，一键运行

**更便捷的升级和扩缩容**

**更简单的系统运维**

**更高效的计算资源利用：**

docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器实例！通过隔离机制将服务器的性能利用到极致

  



### Docker名词

**镜像（image）：**

​	docke镜像就好比一个模板，可以通过这个模板来创建容器服务。

​	例如：tomcat镜像–》run–》tomcat001容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或项目运行就是在容器中的）

**容器（container）：**
	docker利用容器技术，独立运行一个或者一个组应用，通过镜像来创建

​	启动，停止，删除，基本命令！
​	目前就可以把这个容器理解为一个简易的Linux系统

**仓库（repository）：**
	仓库就是存放镜像的地方！分为：共有仓库和私有仓库

​	Docker Hub（默认是国外的）	

​	阿里云也有容器服务器（配置镜像加速！）

​	





> docker安装
>
> ==要在超级用户下进行安装==

```shell
# 1.卸载旧的版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 2. 需要的安装包
yum install -y yum-utils

# 3. 设置镜像仓库
yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 默认是国外的。很慢

使用阿里云的镜像仓库
yum install -y yum-utils
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum软件包索引
[root@master KG]# yum makecache fast

# 4.安装docker相关的内容  ce：社区版 ee：企业版
yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 5.启动docker
systemctl start docker

# 6.查看是否安装成功
[root@master KG]# docker version 

```



```shell
# 7.hello-world
docker run hello-world
```



![image-20230513205146249](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230513205146249.png)



​	==docker run 的运行流程图==

![image-20230513221608133](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230513221608133.png)



```shell
# 8.查看下载的这个hello-world镜像
[root@master KG]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED      SIZE
hello-world   latest    9c7a54a9a43c   8 days ago   13.3kB
```



==/var/lib/docker     docker的默认工作路径==





### 阿里云镜像加速



在阿里云官网找到镜像加速器

![image-20230513210323631](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230513210323631.png)



配置使用即可

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://v3cty438.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```







### 底层原理

docker是一个Client-Server结构的系统，docker的守护进程运行在主机上。通过socket从客户端访问

DockerServer收到Docker-Client的指令，就会执行这个命令

![image-20230513222335730](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230513222335730.png)



### Docker为什么比虚拟机快？

1.docker有比虚拟机更少的抽象层

2.docker利用的是宿主机的内核，虚拟机需要的是Guest OS

所以说新建一个容器的时候，docker不需要向虚拟机一样重新加载一个操作系统内核，利用的是宿主机的操作系统，运行速度是**秒级**；虚拟机是加载Guest OS，运行速度是**分钟级**

![image-20230513222535953](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230513222535953.png)





**虚拟机系统中应用程序的运行原理**

![image-20230514004241281](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514004241281.png)





**容器中应用程序的运行原理**

![image-20230514004407748](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514004407748.png)
