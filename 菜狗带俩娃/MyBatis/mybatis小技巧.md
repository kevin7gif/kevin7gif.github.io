mybatis小技巧
1. #{} 和 ${} 的区别
    * #{}：底层使用PreparedStatement。特点：先进行SQL语句的编译，然后给SQL语句的占位符问号 ？ 传值，可以避免SQL注入
    * ${}：底层使用Statement。特点：先进行SQL语句的拼接，然后再对SQL语句进行编译。有SQL注入的风险
    * **优先使用 #{}，这是原则**
    * 如果需要**SQL语句的关键字**（例如：升序asc，降序desc）放到SQL语句中，**只能使用${}**，因为#{}是以值的形式放到SQL语句中的。
    
2. 向SQL语句中拼接表名，就需要使用${}
   * 日志表：专门存储日志信息的，如果每天生成一张新表，每张表以当天日期作为名称，例如：
      - t_log_20230130
      - t_log_20230331
      - ....
   * 如果想知道某一天的日志信息，就需要将当天的日期拼接到t_log_后面
   * 例如：今天是20230331，就将20230331拼接到t_log_后面
   
3. 批量删除：一次删除多条记录
   * 两种写法：
      - 第一种or：delete from t_car where id=1 or id=2 or id=3;
      - 第二种in：delete from t_car where id in(1,2,3);
   * 例如：delete from t-car where id in(${传入的多个id号})
   
4. 模糊查询：like
   * 需求：根据汽车品牌进行模糊查询
      - select * from t_car where brand like '%奔驰%';
      - select * from t_car where brand like '%比亚迪%'
   * 第一种方案：
      - '%${brand}%'
   * 第二种方案：concat函数，是mysql当中的一个函数，专门进行字符串拼接
     - concat('%',#{brand},'%')
   * 第三种方案：
      - concat('%','${brand}','%')
   * 第四种方案：
      - concat("%",#{brand},"%") -----> 使用较多
   
5. 起别名
    * 第一种方法：在mybatis-config.xml文件中可以使用 typeAlias标签对类起别名，这样的话，在SqlMapper.xml文件中书写sql语句的时候，标签里面的resultType属性就可以使用别名
    * 例如：``` <typeAlias type="caom.xiehn.mybatis.bean.Car" alias="aaa"/>```
        - 上述代码中的type指定给哪个类型起别名
        - 上述代码中的alias指定别名
    * 但是不建议这么做，代码的可读性会降低
    * 第二种方法：使用package标签 
        - ```<package name="com.xiehn.mybatis.bean">```
        - **上述代码默认会对所有的类自动起别名，使用简名作为别名，所有起别名方式都不区分大小写**
    * **namespace不能起别名，只能写全限定类名**
    
6. mybatis-config.xml文件中的mapper属性：
    * ```<mapper resource="CarMapper.xml"/>  要求类的根路径下必须有：CarMapper.xml```
    
    * ```<mapper url="file:///d:/CarMapper.xml"/>  要求在D盘目录下要有CarMapper.xml文件```

    * ```<mapper class="全限定接口名，带有包名"/>```
    
    * mapper标签的属性可以有三个：
        - resource：从类的根目录下开始查找资源，配置文件需要放到类的根路径中
        - url：采用绝对路径的方式，不要求配置文件必须放到类路径中，但是移植性太差，很少使用
        - class：提供的是mapper接口的全限定接口名，必须带包名，**也就是说如果采用这种方式，必须保证CarMapper.xml文件和CarMapper接口在同一目录下**
        
    * 一般直接在mappers标签中使用package属性，这种最常用
        - ```<package name="com.xiehn.mybatis.mapper">```
        
        - 这种方法也必须保证SqlMapper.xml文件和SqlMapper接口在同级目录下
          - **同级目录：java目录和resource目录都是根路径，所有分别在这两个目录下新建com.xiehn.mybatis.bean包，将SqlMapper.xml文件放到resource的包下，将SqlMapper接口放在java的包下**
          
            
    
    提醒！！！！！！
        在idea的resource目录下新建多重目录的话，必须采用/的方式：com/xiehn/mybatis/mapper。
        不能采用 . 的方式
    
    
    
7. ### resultMap结果映射
   
    * 当查询结果的列名和java对象的属性名对应不上怎么办？
        - 第一种方式：as 给列起别名
        - 第二种方式：使用resultMap进行结果映射
        - 第三种方式：是否开启驼峰命名自动映射（在mybatis-config.xml中配置settings标签）
    * 使用resultMap进行结果映射：
    * ```xml
        <!--
        resultMap:
            id：这个结果映射的标识，作为select标签的resultMap属性的值。
            type：结果集要映射的类。可以使用别名。
        -->
        <resultMap id="carResultMap" type="car">
          <!--对象的唯一标识，官方解释是：为了提高mybatis的性能。建议写上。-->
          <id property="id" column="id"/>
        
          <result property="carNum" column="car_num"/>
          <!--当属性名和数据库列名一致时，可以省略。但建议都写上。-->
          <!--javaType用来指定属性类型。jdbcType用来指定列类型。一般可以省略。-->
          <result property="brand" column="brand" javaType="string" jdbcType="VARCHAR"/>
          <result property="guidePrice" column="guide_price"/>
          <result property="produceTime" column="produce_time"/>
          <result property="carType" column="car_type"/>
        </resultMap>
        
        <!--resultMap属性的值必须和resultMap标签中id属性值一致。-->
        <select id="selectAllByResultMap" resultMap="carResultMap">
          select * from t_car
        </select>
      ```
    * **开启驼峰命名自动映射**
        - 使用这种方式的前提是：属性名遵循Java的命名规范，数据库表的列名遵循SQL的命名规范。
        - Java命名规范：首字母小写，后面每个单词首字母大写，遵循驼峰命名方式。
        - SQL命名规范：全部小写，单词之间采用下划线分割。
        - 例如：carNum ----> car_num       produceTime ----> produce_time
        - 在mybatis-config.xml文件中进行配置，settings标签写在environments标签上面
        - ```xml
            <!--放在properties标签后面-->
            <settings>
              <setting name="mapUnderscoreToCamelCase" value="true"/>
            </settings>
          ```

---

```xml
如果显示版本不兼容问题，需要手动在pom.xml文件里面配置jdk版本

<properties>
	<maven.compiler.source>16</maven.compiler.source>
	<maven.compiler.target>16</maven.compiler.target>
</properties>


如果用的tomcat10，则需要把javax包改为jakarta包

```



**关于动态生成Dao类**：---> 使用这种方法可以省去在项目中编写dao包

* mybatis中提供了相关的机制，可以动态生成dao接口的实现类。（代理类：dao接口的代理）
* mybatis中实际上采用了代理模式。在内存中生成dao接口的代理类，然后创建类的实例
* **使用mybatis这种代理机制的前提：SqlMapper.xml文件中namespace必须是dao接口的全限定名称，id必须是dao接口中的方法名**

getMapper()方法------> 动态生成类

例如：**private AccountDao accountDao = SqlSession.getMapper(AccountDao.class)** 
