## 多对一映射关系设计

![005-多对一的理解.png](https://cdn.nlark.com/yuque/0/2023/png/27467446/1676294451674-33786d10-f116-45e9-9004-d6b4eec3fc76.png)



常见的三种方式

### 第一种方式：

**一条SQL语句，级联属性映射。**

先在SqlMapper.xml文件里面配置如下信息：

```xml
<resultMap id="studentResultMap" type="Student">
        <id property="sid" column="sid"/>
        <result property="sname" column="sname"/>
        <result property="clazz.cid" column="cid"/>
        <result property="clazz.cname" column="cname"/>
</resultMap>
    <select id="selectById" resultMap="studentResultMap">
        select
            t_stu.sid,t_stu.sname,t_clazz.cid,t_clazz.cname
        from
            t_stu left join t_clazz  on t_stu.cid = t_clazz.cid
        where t_stu.sid=#{sid}
    </select>
```

在StudentMapper接口中添加方法：

```java
/**
     * 根据id获取学生信息，同时获取学生关联的班级对象
     * @param id 学生的id
     * @return 学生对象，但是学生对象中包含班级对象
     */
    Student selectById(Integer id);
```



然后在test包下写一个测试类：

```java
@Test
    public void testSelectById(){
        SqlSession sqlSession= SqlSessionUtil.openSession();
        StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
        Student student=mapper.selectById(1);
        System.out.println(student.getSid());
        System.out.println(student.getSname());
        System.out.println(student.getClazz().getCid());
        System.out.println(student.getClazz().getCname());
        sqlSession.close();
    }
```



### 第二种方式：

**一条SQL语句，association。**

其他位置都不需要修改，只需要修改resultMap标签中的配置：association即可

```xml
<resultMap id="studentResultMapAssociation" type="Student">
        <result property="sid" column="sid"/>
        <result property="sname" column="sname"/>
        <!--
            association：关联，一个Student对象关联一个Clazz对象
            property：提供要映射的bean类的属性名
            javaType：用来指定要映射的java类型
        -->
        <association property="clazz" javaType="Clazz">
            <id property="cid" column="cid"/>
            <result property="cname" column="cname"/>
        </association>
</resultMap>
    <select id="selectByIdAssociation" resultMap="studentResultMapAssociation">
        select
            t_stu.sid,t_stu.sname,t_clazz.cid,t_clazz.cname
        from
            t_stu left join t_clazz  on t_stu.cid = t_clazz.cid
        where t_stu.sid=#{sid}
    </select>
```

在StudentMapper接口中添加方法

```java
 /**
     * 一条sql语句，association
     * @param id
     * @return
     */
    Student selectByIdAssociation(Integer id);
```



```java
 @Test
    public void testSelectByIdAssociation(){
        SqlSession sqlSession= SqlSessionUtil.openSession();
        StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
        Student student=mapper.selectById(1);
        System.out.println(student);
        sqlSession.close();
    }
```



### 第三种方式：

**两条SQL语句，分步查询。（这种方式常用：优点一是可复用。优点二是支持懒加载。）**

 

其他位置不需要修改，只需要修改以及添加以下三处：
第一处：association中select位置填写sqlId。sqlId=namespace+id。其中column属性作为这条子sql语句的条件。

```xml
 <resultMap id="selectByIdStep" type="Student">
        <id property="sid" column="sid"/>
        <result property="sname" column="sname"/>
        <association property="clazz"
                     select="com.xiehn.mybatis.mapper.ClazzMapper.selectByIdStep2"
                     column="cid"/>
</resultMap>
    <select id="selectByIdStep1" resultMap="selectByIdStep">
        select * from t_stu where sid=#{sid}
    </select>
```

第二处：在ClazzMapper接口中添加方法：

```java
 /**
     * 分布查询第二步：根据cid获取班级信息
     * @param cid
     * @return
     */
    Clazz selectByIdStep2(Integer cid);
```

在StudentMapper接口中添加方法

```java
/**
 * 分布查询第一步：先根据学生的sid查询学生的信息
 * @param sid
 * @return
 */
Student selectByIdStep1(Integer sid);
```

第三处：在ClazzMapper.xml文件中进行配置

```xml
<select id="selectByIdStep2" resultType="Clazz">
        select * from t_clazz where cid=#{cid}
</select>
```

**分步优点：**
●第一个优点：代码复用性增强。一个大步拆成多个小步，每一个小步也可以重复利用
●第二个优点：支持延迟加载（懒加载）。【暂时访问不到的数据可以先不查询。提高程序的执行效率】





### 延迟加载

延迟加载的核心原理：用的时候再执行查询语句，不用的时候不查询

作用：提高性能，尽可能的不查或者少查来提高效率

在association标签中添加 fetchType="lazy"（这种配置是局部的设置，只对当前association关联的sql语句起作用）

默认情况下是不开启延迟加载的



实际开发中，大部分都是需要使用延迟加载的，所有建议开启全局的延迟加载机制

* 在mybatis核心配置文件中添加全局配置：lazyLoadingEnabled=true

  

**实际开发中的模式：**

​		把全局的延迟加载打开

​		如果某一步不需要使用延迟加载，在association标签中设置 fetchType="eager"



---

## 一对多映射关系设计

![006一对多关系映射原理.png](https://cdn.nlark.com/yuque/0/2023/png/27467446/1676339779911-ea65192f-5a63-4597-ace7-cbde842a8cea.png)



### 第一种方式

**collection**

```java
/**	 * ClazzMaper的接口
     * 根据班级编号查询班级信息
     * @param cid
     * @return
     */
    Clazz selectByCollection(Integer cid);
```



```xml
<resultMap id="clazzResultMap" type="Clazz">
        <id property="cid" column="cid"/>
        <result property="cname" column="cname"/>
        <!--一对多，这里是collection。就是集合的意思-->
        <!--ofType用来指定集合中元素的类型-->
        <collection property="stus" ofType="Student">
            <id property="sid" column="sid"/>
            <result property="sname" column="sname"/>
        </collection>
    </resultMap>
    <select id="selectByCollection" resultMap="clazzResultMap">
        select t_clazz.cid,t_clazz.cname,t_stu.sid,t_stu.sname from t_clazz
            left join t_stu  on t_clazz.cid = t_stu.cid
                where t_clazz.cid=#{cid}
    </select>
```



```java
ClazzMapperTest测试类 
@Test
    public void testSelectByCollection(){
        SqlSession sqlSession= SqlSessionUtil.openSession();
        ClazzMapper mapper = sqlSession.getMapper(ClazzMapper.class);
        Clazz clazz = mapper.selectByCollection(1001);
        System.out.println(clazz);
        sqlSession.close();
    }
```



### 第二种方式

**分步查询**

```java
/**  * ClazzMapper接口
     * 分步查询第一步：根据班级编号查询班级信息
     * @param cid
     * @return
     */
    Clazz selectByStep1(Integer cid);
```



```xml
<!--分步查询第一步：根据班级的cid获取班级信息-->
<!--ClazzMapper.xml配置文件-->
    <resultMap id="clazzResultMapStep" type="Clazz">
        <id property="cid" column="cid"/>
        <result property="cname" column="cname"/>
        <collection property="stus"
                    select="com.xiehn.mybatis.mapper.StudentMapper.selectByCidStep2"
                    column="cid"/>
    </resultMap>
    <select id="selectByStep1" resultMap="clazzResultMapStep">
        select cid,cname from t_clazz where cid = #{cid}
    </select>
```



```xml
<!--StudentMapper.xml配置文件-->
<select id="selectByCidStep2" resultType="Student">
        select * from t_stu where cid= #{cid}
</select>
```



```java
ClazzMapperTest测试类
@Test
    public void testSelectByStep1(){
        SqlSession sqlSession=SqlSessionUtil.openSession();
        ClazzMapper mapper = sqlSession.getMapper(ClazzMapper.class);
        Clazz clazz=mapper.selectByStep1(1002);
        System.out.println(clazz);
        sqlSession.close();
    }
```

