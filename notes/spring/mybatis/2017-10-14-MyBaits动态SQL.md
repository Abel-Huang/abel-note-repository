# MyBaits动态SQL
 
## if
``` OGNL
<select id="getDeptLikeByName" parameterType="string" resultMap="deptMap">
        select id, dept_id, dept_name from dept where 1=1
        <if test="_parameter != null and _parameter != '' " >
        and dept_name like concat('%', #{deptName}, '%')
        </if>
</select>
```
注意\_parameter的使用，如果直接使用deptName，会报错，
`org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'deptName' in 'class java.lang.String `
 
## choose、when、 otherwise
```OGNL
<select id="getDeptByIdOrName" parameterType="dept" resultMap="deptMap">
         select id, dept_id, dept_name from dept where 1=1
                <choose>
                        <when test="deptId > 0">
                                and dept_id = #{deptId}
                        </when>
                        <when test="deptName != null and deptName != '' " >
                                and dept_name like concat('%', #{deptName}, '%')
                        </when>
                        <otherwise>
                                and  id > 0
                        </otherwise>
                </choose>
</select>
```
 
## trim、 where、 set
不知道怎么表述了，where用法如下
```OGNL
<select id="getDeptLikeByName" parameterType="string" resultMap="deptMap">
                select id, dept_id, dept_name from dept
                <where>
                        <if test="_parameter != null and _parameter != '' " >
                                and dept_name like concat('%', #{deptName}, '%')
                        </if>
                </where>
 
        </select>
```
trim用法类似，也是用于去除一些SQL的特殊字段；
set主要用于更新一个表中的部分字段，如果在更新表把所有的字段都传输回服务器，对性能影响很大。
```OGNL
<update id="updateDept" parameterType="Dept">
                update dept
    <set>
        <if test="deptId > 0 ">
                                dept_id = #{deptId},
                        </if>
                        <if test="deptName != null and deptName !='' ">
                                dept_name = #{deptName}
                        </if>
                </set>
     where id = #{id}
</update>
```
## foreach
foreach用于遍历集合, 往往用于SQL语句中的in语句。
```OGNL
  <select id="listDept" resultMap="deptMap">
                select *  from dept where id in
                <foreach item = "item" index = "index" collection="list"
                         open="(" separator="," close=")">
                        #{item}
                </foreach>
        </select>
```
## test
主要用于条件判断。
## bind
主要用于自定义一个上下文变量，如模糊查询，可以方便的在其他地方使用。
```OGNL
  <select id="getDeptLikeByName" parameterType="string" resultMap="deptMap">
                <bind name="pattern" value="'%' + _parameter +'%'"/>
                select id, dept_id, dept_name from dept
                <where>
                        <if test="_parameter != null and _parameter != '' " >
                                and dept_name like #{pattern}
                        </if>
                </where>
 
        </select>
```