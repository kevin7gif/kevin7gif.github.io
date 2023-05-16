ssm整合步骤：

* 新建maven项目，选择webapp模板
* 修改目录
* 修改pom.xml文件
* 添加jdbc.properties属性文件
* 添加SqlMapper.xml文件（使用模板）
* 添加applicationContext_mapper.xml文件（数据访问层的核心配置文件）
* 添加applicationContext_service.xml文件（业务逻辑层的核心配置文件）
* 添加springmvc.xml文件
* 在web.xml文件中注册springmvc框架，注册spring框架
* 新建实体类user
* 新建UserMapper接口
* 新建UserMapper.xml实现增删改查
* 新建service接口和实现类
* 新建控制器，完成所有功能
* 浏览器测试功能