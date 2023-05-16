MyBatis参数处理
1. ### 单个简单类型参数
   
    * 简单类型包括：
      - byte short int long float double char
      - Byte Short Integer Long Float Double Character
      - String
      - java.util.Date
      - java.sql.Date
    * 需求：根据name查、根据id查、根据birth查、根据sex查
    * 在SqlMapper.xml文件里写CRUD语句的时候，CRUD标签里有一个属性叫做：parameterType（用来指定参数的类型）
      - 例如： --------->  该参数也支持别名，不用手动配置，自动就有
      - java.lang.String可以写成string
      - ```xml
        <select id="selectByName" resultType="student" parameterType="java.lang.String">
                        select * from t_student where name = #{name}
        </select>
        ```
2. 如果是Map参数的话，sql语句中 #{} 的位置，写的就是Map集合的key
   ```xml
   <select id="selectByParamMap" resultType="student">
             select * from t_student where name = #{nameKey} and age = #{ageKey}
   </select>```
3. 如果是实体类参数的话，sql语句中 #{} 的位置，写得就是实体类的属性
   ```xml
   <insert id="insert">
     insert into t_student values(null,#{name},#{age},#{height},#{birth},#{sex})
   </insert>
   ```
4. ### 多参数类型
   
   - 需求：根据name和sex查询student信息
   - List<Student> selectByNameAndSex(String name, Character sex);
   - 多参数的话，mybatis底层会自动创建一个Map集合，并且Map集合是以这种方式存储参数的：
     - map.put("arg0",name);
     - map.put("arg1",sex);
     - map.put("param1",name);
     - map.put("param2",sex);
   - 在SqlMapper.xml文件中采用如下方式编写：
     - ```xml
         <select id="selectByNameAndSex" resultType="student">
         <!--select * from t_student where name = #{name} and sex = #{sex}-->
             select * from t_student where name = #{arg0} and sex = #{arg1}
         </select>
       ```
5. ### param注解
   
   * 需求：根据name和age查询student信息
   * List<Student> selectByNameAndAge(@Param(value="name") String name, @Param("age") int age);
   * ```xml
     <select id="selectByNameAndAge" resultType="student">
       select * from t_student where name = #{name} and age = #{age}
     </select>
     ```
