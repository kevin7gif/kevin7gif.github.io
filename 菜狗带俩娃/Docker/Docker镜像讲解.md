### 镜像是什么

镜像是一种轻量级、可执行的软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件

所有的应用，直接打包docker镜像，就可以直接跑起来

**如何得到镜像：**

* 从远程仓库下载
* 通过他人拷贝
* 自己制作一个镜像 DockerFile





### 镜像加载原理



> UnionFS（联合文件系统）

下载的时候看到的一层层的代码就是这个！（一步一步的进行分层）

UnionFS（联合文件系统）：是一种分层、轻量级且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。Union文件系统是docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像

**特性：**一次同时加载多个文件系统，但从外面看，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录





> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统称为UnionFS

**bootfs**（boot file system）主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在docker镜像的最底层就是bootfs。这一层与典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，则是系统也会卸载bootfs。**是大家公用的**

**rootfs**（root file system），在bootfs之上，包含的就是典型Linux系统中的/dev  /proc  /bin /etc等标准文件和目录。rootfs就是各种不同的操作系统发行版，比如：centos，ubuntu

![image-20230514104126546](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514104126546.png)



平时安装虚拟机的centos有好几个G，但是docker才200多M

**对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令、工具和程序库就可以。因为底层直接用Host和kernel，自己只需要提供一个rootfs就可以，由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。**





> 分层讲解

下载镜像时可以看到是一层一层的

![image-20230514104922646](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514104922646.png)



![image-20230514150546946](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514150546946.png)





**每一层的文件都不会重复**

![image-20230514150713332](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514150713332.png)







![image-20230514150812861](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514150812861.png)





![image-20230514151024307](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514151024307.png)





> 特点

docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是通常说的容器层，容器之下的都叫镜像层

例如：

![image-20230514151401979](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514151401979.png)







### Commit镜像（提交自己的镜像）



``` shell
docker commit  提交容器成为一个新的副本

# 命令和git原理类似
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：[TAG]
```



测试：

```shell
1. 启动一个默认的tomcat容器

2. 发现这个默认的tomcat是没有webapps应用的，这是镜像的原因，因为官方的镜像默认webapps下是没有文件的！

3.自己将webapps.list下的所有文件拷贝到webapps目录下

4.将我们操作过的容器通过commit提交为一个镜像！以后直接使用这个镜像即可！这就是我们自己的一个修改过的镜像
```



![image-20230514152410594](C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20230514152410594.png)





**弱小和无知不是生存的障碍，傲慢才是！！！**

