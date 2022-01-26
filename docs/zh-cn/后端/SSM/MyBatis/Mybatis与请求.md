# Mybatis

## 动态SQL

普通式

```xml
<insert id="insertRole" parameterType="com.edu.ssm.po.Role">
	<!--<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
		select LAST_INSERT_ID() 
	</selectKey>-->
	  insert into Role(username,birthday,sex,address) 
	  values(#{username},#{birthday},#{sex},#{address})
	</insert>
```

### 使用动态sql trim标签，集齐了where set的强大标签

```xml
<insert id="insertRole" parameterType="com.edu.ssm.po.Role">
	  insert into Role
	 <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null and id!='' ">
        id,
      </if>
      <if test="username != null">
        username,
      </if>
      <if test="birthday != null">
        birthday,
      </if>
      <if test="sex != null">
        sex,
      </if>
      <if test="address != null">
        address,
      </if>
    </trim>
	  <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
        #{id,jdbcType=INTEGER},
      </if>
      <if test="username != null">
        #{username,jdbcType=VARCHAR},
      </if>
      <if test="birthday != null">
        #{birthday,jdbcType=DATE},
      </if>
      <if test="sex != null">
        #{sex,jdbcType=CHAR},
      </if>
      <if test="address != null">
        #{address,jdbcType=VARCHAR},
      </if>
    </trim>
	</insert>
```

### 使用注解的方式注入对象

```java
List<Role> findRoleByOj(@Param("role") Role role);
```

```xml
<select id="findRoleByOj" parameterType="com.edu.ssm.po.Role" resultType="com.edu.ssm.po.Role">
		select * from role 
		<where>
		<if test="role.id!=null and role.id!='' "> <!--这个要注意-->
		 and id = #{role.id}
		 </if>
		</where>	 
	</select>
```

没有注解的方式

```java
List<Role> findRoleByOj(Role role);
```

```xml
<select id="findRoleByOj" parameterType="com.edu.ssm.po.Role" resultType="com.edu.ssm.po.Role">
		select * from role 
		<where>
		<if test="id!=null and id!='' "> <!--这个要注意-->
		 and id = #{id}
		 </if>
		</where>	 
	</select>
```



## 模板

### mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 <mapper namespace="com.edu.ssm.mapper.RoleMapper"> 

</mapper>
```

### src/SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 和spring整合后 environments配置将废除-->
	<environments default="development">
		<environment id="development">
		<!-- 使用jdbc事务管理-->
			<transactionManager type="JDBC" />
		<!-- 数据库连接池-->
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url" value="jdbc:mysql://localhost:3306/springmvc?characterEncoding=utf-8" />
				<property name="username" value="root" />
				<property name="password" value="123456" />
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="com/edu/ssm/mapper/ItemsMapper.xml"/>
		<mapper resource="com/edu/ssm/mapper/RoleMapper.xml"/>
	</mappers>
</configuration>
```



当 SQL 语句中的元数据（如表名或列名）是动态生成的时候，字符串替换将会非常有用。 举个例子，如果你想通过任何一列从表中 select 数据时，不需要像下面这样写： 

```java
@Select("select * from user where id = #{id}")
User findById(@Param("id") long id);

@Select("select * from user where name = #{name}")
User findByName(@Param("name") String name);

@Select("select * from user where email = #{email}")
User findByEmail(@Param("email") String email);

// and more "findByXxx" method可以只写这样一个方法： 
@Select("select * from user where ${column} = #{value}")
User findByColumn(@Param("column") String column, @Param("value") String value);其中 ${column} 会被直接替换，而 #{value} 会被使用 ? 预处理。 因此你就可以像下面这样来达到上述功能： 
User userOfId1 = userMapper.findByColumn("id", 1L);
User userOfNameKid = userMapper.findByColumn("name", "kid");
User userOfEmail = userMapper.findByColumn("email", "noone@nowhere.com");
```

动态sql 

choose
	when 
	when
	otherwise 
与if不同的是
	choose只能是单选
	执行完一个就不在找了
	if还在继续执行

where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，where 元素也会将它们去除。

如果 where 元素没有按正常套路出牌，我们可以通过自定义 trim 元素来定制 where 元素的功能。比如，和 where 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```




使用if 如果有的值吗没有传就多出来一个and

所以使用< where> 来取消前面多于的and

```xml
<select id="findActiveBlogLike" resultType="Blog">
      SELECT * FROM BLOG
  <where>
    <if test="state != null and state !='' ">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

## ResultMap 通常在联合查询使用

```xml
  <resultMap id="BaseResultMap" type="com.example.mybatis.entity.Items">
    <result column="id" jdbcType="INTEGER" property="id" />
    <result column="name" jdbcType="VARCHAR" property="name" />
    <result column="price" jdbcType="REAL" property="price" />
    <result column="pic" jdbcType="VARCHAR" property="pic" />
    <result column="createtime" jdbcType="TIMESTAMP" property="createtime" />
  </resultMap>
```

# 增

```sql
INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN );
```

```xml
   <insert id="insert">
        insert  into school
        <trim prefix="(" suffix=")" suffixOverrides=",">
            <if test="province != null">
                province,
            </if>
            <if test="code != null">
                code,
            </if>
            <if test="major != null">
                major,
            </if>
            <if test="schoolcode != null">
                schoolcode,
            </if>
            <if test="schoolname != null">
                schoolname,
            </if>
            <if test="year != null">
                year,
            </if>
        </trim>
        <trim prefix="values (" suffix=")" suffixOverrides=",">
            <if test="province != null">
                #{province},
            </if>
            <if test="code != null">
                #{code},
            </if>
            <if test="major != null">
                #{major},
            </if>
            <if test="schoolcode != null">
                #{schoolcode},
            </if>
            <if test="schoolname != null">
                #{schoolname},
            </if>
            <if test="year != null">
               #{year},
            </if>
        </trim>
    </insert>
```



# 删

```sql
DROP TABLE table_name ; 删除整个表，包括表结构
```

```sql
删除表内数据，用 delete。格式为：
delete from 表名 where 删除条件;

实例：删除学生表内姓名为张三的记录。
delete from  student where  T_name = "张三";

清除表内数据，保存表结构，用 truncate。格式为：
truncate table 表名;

实例：清除学生表内的所有数据。
truncate  table  student;
```



# 改

```sql
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause]
```

```xml
 <update id="update">
        update school
        <set>
            <if test="province != null">
                province=#{province},
            </if>
            <if test="code != null">
                code=#{code},
            </if>
            <if test="major != null">
                major=#{major},
            </if>
            <if test="schoolcode != null">
                schoolcode=#{schoolcode},
            </if>
            <if test="schoolname != null">
               schoolname= #{schoolname},
            </if>
            <if test="year != null">
                year=#{year},
            </if>
    </set>
        where  id=#{id}
    </update>
```

# 查

模糊查询

```xml
        <select id="selectprovince" resultType="com.edu.entity.School">
            select * from school where province like concat('%',#{province},'%');
        </select>
```

```sql
SELECT column_name,column_name
FROM table_name
[WHERE Clause]
[LIMIT N][ OFFSET M]
```

```xml
select _column,_column from _table [where Clause] [limit N][offset M]
 select * : 返回所有记录
 limit N : 返回 N 条记录
 offset M : 跳过 M 条记录, 默认 M=0, 单独使用似乎不起作用
 limit N,M : 相当于 limit M offset N , 从第 N 条记录开始, 返回 M 条记录
实现分页：

select * from _table limit (page_number-1)*lines_perpage, lines_perpage

或

select * from _table limit lines_perpage offset (page_number-1)*lines_perpage
```

```sql
/*websites  表名   NAME alexa url country  字段*/
SELECT * FROM websites;                      /* 查询表所有数据 */

SELECT NAME FROM websites;                   /* 查询表字段数据 */

SELECT * FROM websites where name = "广西";   /* 查询表字段下条件数据 */

SELECT * from websites where name like "_o%"; /* 模糊查询表下数据 */

SELECT * FROM websites where id BETWEEN "1" AND "5";    /* 查询表下字段范围数据 */

SELECT * FROM websites WHERE name in ("广西","百度");    /* 查询表字段下固定条件数据 */

SELECT DISTINCT country FROM Websites;                  /* 查询去重值 */

SELECT * FROM Websites WHERE country = "CN" AND alexa > 50;  /*查询表下范围条件数据*/

SELECT * FROM Websites WHERE country = "USA" OR country="sh"; /* 查询表下条件不同值 */

SELECT * FROM Websites ORDER BY alexa;                      /* 查询表下值排序结果 */

SELECT * FROM Websites ORDER BY alexa DESC;                 /* 查询表下排序结果降序 */

SELECT * FROM Websites LIMIT 2;      /* 查询表下范围数据 */

SELECT name as zzz from websites;    /*别名查询表下数据*/
```

## 多表关联查询

```sql
select 字段 from 表1 left join 表2 on  条件 (一般为表1与表2的关联条件)
```



## yml基本模板

```yml
server: 
	port: 8080
	
spring:  
 	datasource:   
        username: root    
        password: 123456   
        url: jdbc:mysql://localhost:3306/springmvc?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC    
    	driver-class-name: com.mysql.cj.jdbc.Drivermybatis:
    #一定不能错，要不然Mapper扫描不到，在主类配置MapperScan  
    
mapper-locations: classpath:mapper/*.xml  
    type-aliases-package: com.edu.entity
    
resources:    
    static-locations: ["/templates/","/static/",]
    
thymeleaf:
    prefix: classpath:/templates/    
    suffix: .html    
    cache: false
```



## SQL HAVING 子句

HAVING 子句

在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与聚合函数一起使用。

HAVING 子句可以让我们筛选分组后的各组数据。

### SQL HAVING 语法

```sql
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name
HAVING aggregate_function(column_name) operator value;
```

# xml映射文件

## 结果映射

```xml
  <resultMap type="com.edu.entity.Weapon" id="weaponMap">
         <id column="id" property="id"/>
         <result column="warname" property="warname" />
         <result column="userid" property="userid" />
     </resultMap>

  <resultMap type="com.edu.entity.User" id="userResultMap">
    <id property="id" column="id"/>
      <result property="loginId" column="loginId" />
      <result property="userName" column="userName"/>
      <result property="note" column="note"/>
            <!--assocication可以指定联合的JavaBean对象 
                property="role"指定哪个属性是联合的对象
                javaType:指定这个属性对象的类型
            -->
            <!--一对一查询-->
      <association property="role" javaType="com.edu.entity.Role">
      <!-- com.edu.entity.Role中的role属性 -->
        <id column="role_id" property="id"/>
        <result column="roleName" property="roleName"/>
      </association>
  </resultMap>
  
  <resultMap type="com.edu.entity.User" id="userweapon">
    <id property="id" column="id"/>
      <result property="loginId" column="loginId" />
      <result property="userName" column="userName"/>
      <result property="note" column="note"/>
           <!--一对多查询-->
    <collection property="weapon" resultMap="weaponMap">
   
    </collection>
  </resultMap>
```

Weapon.java

```java
package com.edu.entity;

public class Weapon {
  private int id;
  private String warname;
  private int userid;

  @Override
  public String toString() {
    return "Weapon [id=" + id + ", warname=" + warname + ", userid=" + userid + "]";
  }
  public int getId() {
    return id;
  }
  public void setId(int id) {
    this.id = id;
  }
  public String getWarname() {
    return warname;
  }
  public void setWarname(String warname) {
    this.warname = warname;
  }
  public int getUserid() {
    return userid;
  }
  public void setUserid(int userid) {
    this.userid = userid;
  }


}

```

Role.java（集合类）

```java
package com.edu.entity;

/**
 * @since 2017-08-08
 * @author queen
 * 定义一个Java类Role
 *
 */
public class Role {
  private int id;
  private String roleName;

  public int getId() {
    return id;
  }

  public void setId(int id) {
    this.id = id;
  }

  public String getRoleName() {
    return roleName;
  }

  public void setRoleName(String roleName) {
    this.roleName = roleName;
  }

        public Role(){

        }

  public Role(int id, String roleName) {
    this.id = id;
    this.roleName = roleName;
  }

  @Override
  public String toString() {
    return "Role [id=" + id + ", roleName=" + roleName + "]";
  }

}

```

#动态sql
##if
动态 SQL 通常要做的事情是根据条件包含 where 子句的一部分。比如：

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

这条语句提供了一种可选的查找文本功能。如果没有传入“title”，那么所有处于“ACTIVE”状态的BLOG都会返回；反之若传入了“title”，那么就会对“title”一列进行模糊查找并返回 BLOG 结果（细心的读者可能会发现，“title”参数值是可以包含一些掩码或通配符的）。

如果希望通过“title”和“author”两个参数进行可选搜索该怎么办呢？首先，改变语句的名称让它更具实际意义；然后只要加入另一个条件即可。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

choose, when, otherwis

## choose, when, otherwise

有时我们不想应用到所有的条件语句，而只想从中择其一项。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

还是上面的例子，但是这次变为提供了“title”就按“title”查找，提供了“author”就按“author”查找的情形，若两者都没有提供，就返回所有符合条件的 BLOG（实际情况可能是由管理员按一定策略选出 BLOG 列表，而不是返回大量无意义的随机结果）。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

