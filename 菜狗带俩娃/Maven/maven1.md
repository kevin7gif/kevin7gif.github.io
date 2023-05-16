maven可以管理jar文件

自动下载jar的直接依赖，比如：a.jar需要b.jar，maven会自动下载b.jar

管理你需要的jar版本

帮你编译程序，把Java编译为class

帮你测试代码是否正确

帮你打包文件，形成jar文件或者war文件

帮你部署项目

**pom.xml：用于定义和配置项目的文件，包含了项目的基本信息、依赖、插件等，是maven的核心文件，所有项目的配置信息都在这个文件里进行配置。**

**通过om.xml，maven可以自动化的管理项目构建、依赖管理、发布等，简化了Java项目的构建和管理流程**



### 仓库

仓库：用于存储资源，包含各种jar包

仓库分类：

* 本地仓库：自己电脑上存储资源的仓库，连接远程仓库获取的资源
* 远程仓库：非本机电脑上的仓库，为本地仓库提供资源
  * 中央仓库：Maven团队维护，存储所有资源的仓库
  * 私服：部门/公司范围内存储资源的仓库，从中央仓库获取资源

私服的作用：

* 保存具有版权的资源，包含购买或自主研发的jar包

* 一定范围内共享资源，仅对内部开放，不对外共享

  

---

### 坐标

pom：项目对象模型，是一个pom.xml文件

概念：用于描述仓库中资源的位置

Maven坐标主要组成：

* groupId：定义当前Maven项目隶属组织名称（通常是域名反写，例如：org.mybatis）
* artifactld：定义当前Maven项目名称（通常是模块名称，例如：CRM、SMS）
* version：定义当前项目版本号
* packing：打包后压缩文件的扩展名，默认是jar，web应用是war（可以不写）

Maven坐标的作用：

* 使用唯一标识，唯一性定位资源位置，通过该标识可以将资源的识别与下载工作交由机器完成

groupId、artifactId、version决定项目在仓库中的路径，artifactId和version决定jar包的名称

---

Maven命令

mvn	clean：清理，会删除原来编译和测试的目录

mvn	compile：编译主程序，会在当前目录下生成一个target，里面存放编译主程序之后的字节码文件

mvn	test-compile：编译测试程序，会在当前目录下生成一个target，里面存放编译测试程序之后的字节码文件

mvn	test：测试，会生成一个目录surfire-reports，保存测试结果

mvn	package：打包

mvn	install：安装到本地仓库

mvn	deploy：安装到本地仓库并且会保存到私服仓库，还会自动把项目部署到web容器



依赖：dependencies和dependency，相当于是java代码中import

build：maven在进行项目的构建时，配置信息，例如指定编译Java代码使用的jdk的版本

插件：maven命令执行时，真正完成功能的是插件，插件就是一些jar文件

单元测试：用的是junit，junit是一个专门测试的框架（工具）

* 测试的是类中的方法，每一个方法都是独立测试的，方法是测试的基本单位

* maven借助单元测试，批量的测试类中的大量方法是否符合预期

  

---

### IDEA中设置Maven

**在个人CSDN收藏里面有相关信息**



---

### 依赖范围

使用scope表示

* scope值有：compile、test、provided（默认是compile）
  * test：表示只在测试阶段起作用
  * compile：在整个maven项目的所有阶段都起作用
  * provided：仅不参与maven项目的打包和部署阶段
* scope表示依赖范围，也就是在maven构建项目哪些阶段中起作用
  * maven构建项目：清理、编译、测试、打包、安装、部署

* junit的依赖范围

* ```xml
  scope值为test，表示只在测试阶段起作用
         <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.13.2</version>
              <scope>test</scope>
          </dependency>
  ```

* servlet的依赖范围

* ```xml
   	   <dependency>
              <groupId>jakarta.servlet.jsp</groupId>
              <artifactId>jakarta.servlet.jsp-api</artifactId>
              <version>3.0.0</version>
              <scope>provided</scope>
          </dependency>
  ```

* 

---

### maven常用设置

1.maven的属性设置

* <properties>设置maven的常用属性

2.maven的全局变量

3.资源插件