使用mybatis完成CRUD

1. 什么是CRUD
    * C：Creat增
    * R：Retrieve查
    * U：Update改
    * D：Delete删
2. insert
    * JDBC代码中的占位符 ？ 在mybatis中采用的是：#{}
    * java程序中可以使用Map集合给sql语句的占位符传值
      - 使用此方法时，#{}里面写的是Map集合的key值
    * java程序中可以使用bean类给sql语句的占位符传值
      - 使用此方法时，#{}里面写的是bean类的属性名
      - 严格意义上说，写的是get方法的方法名去掉get，然后将剩下的单词首字母小写，然后放进去
        - 例如：getEmail() ---> #{email}
        - 例如：getUserName() ---> #{userName}
        - ....
        - mybatis通过反射机制调用这些属性的get方法获取值
    * 也就是说，mybatis在底层给 ？ 传值的时候，先要通过bean对象的get方法获取值
3. delete
    * 需求：根据id删除数据
        - 将id=11的数据删除
    * 实现
        - int count=sqlSession.delete("deleteById",11);
        - ```
          <delete id="deleteById">
              delete from t_car where id = #{id}
          </delete>
          ```
        - 注意：如果占位符只有一个，那么#{}里面的占位符可以随便写
4. update
    * 需求：根据id修改某条记录
    * 实现
      - //准备数据
        Car car = new Car(4L, "1004", "凯美瑞", 30.3, "1999-11-10", "燃油车");
      - //执行sql语句
        int count = sqlSession.update("updateById", car);
        - ```
           <update id="updateById">
              update t_car
                set car_num=#{carNum},
                    brand=#{brand},
                    guide_price=#{guidePrice},
                    produce_time=#{produceTime},
                    car_type=#{carType}
                where id = #{id}
           </update>
          ```
5. select（查一条数据，根据主键查询）
   * 实现： 
        - ```
          <select id="selectById" resultType="com.xiehn.bean.com.mybatis.Car">
               select * from t_car where id = #{id}
          </select>```
        - Object car = sqlSession.selectOne("selectById", 1);
   * 需要特别注意的是：
        - **select标签中resultType属性用来告诉mybatis，查询结果集封装成什么类型的Java对象。因为实际最后输出的时候会调用指定封装好的java对象的toString方法**
        - **resultType通常写的是：全限定类名，即类的引用 ---> com.xiehn.bean.com.mybatis.Car**
   * Mapper.xml文件中，这样写select语句的问题是：有些字段会查找不出来
   * **原因是bean类中定义的有些属性名和数据库中定义的字段名不一致**，但是如果将两者的名字改为一致，势必会违反一方的命名规范，**这时可以在select语句中用as起别名**
   * ```
        <select id="selectById" resultType="com.xiehn.bean.com.mybatis.Car">
            select id, car_num as carNum, brand, guide_price as guidePrice, produce_time as produceTime, car_type as carType
            from t_car
            where id = #{id}
        </select>
     ```
     
6. select（查所有数据）
    * ```
        <select id="selectAll" resultType="com.xiehn.bean.com.mybatis.Car">
            select id, car_num as carNum, brand, guide_price as guidePrice, produce_time as produceTime, car_type as carType
            from t_car
        </select>
      ```
    * List<Object> cars=sqlSession.selectList("selectAll");
      //遍历
      for(Object car:cars){
        System.out.println(car);
      }
    * 注意：resultType还是要指定要封装的结果集对象。不是指定List类型，是指定List集合中元素的类型
    * selectList方法：mybatis通过这个方法就得知你需要一个List集合，它会自动返回一个List集合
7. 在Mapper.xml文件中有一个**namespace**，这个属性是用来指定命名空间的，用来防止sql_id重复
    - 在Java程序中的写法是：在调用的方法里面参数写成：命名空间.sql_id
    - 例如：假设namespace为：aaaa
      - 则方法要写成：List<Car> cars=sqlSession.selectList("aaaa.selectAll");
    - 例如：假设namespace为：bbbb
      - 则方法要写成：List<Car> cars=sqlSession.selectList("bbbb.selectAll");