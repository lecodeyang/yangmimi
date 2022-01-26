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

