## Test类 testSave.java

```java
import java.util.List;
import org.hibernate.Criteria;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;
import org.hibernate.criterion.Order;
import org.junit.Test;
import com.DB.edu.Student;
public class testSave {
	//执行查找，不能忘记在实体类写一个tostring();
	//删除或修改，一定要先查再操作****，删除的session.delete()里面放的是对象，所以把那个查询到的对象放到里面，只有查询不需要事务，增删改都要事务
	@Test
	public void testSave(){
	// 先加载配置文件默认加载src目录下的配置文件,加载特定名称的配置文件
		Configuration config=new Configuration().configure();
	//1.创建SessionFactory对象,一个数据库只需要一个SessionFactory，创建工具类来实现，		不是轻量级的，它的创建于销毁浪费资源，
	//2.它是对hibernate的初始化，可以生产SessionFactory 重量级对象
	SessionFactory factory = config.buildSessionFactory();
	/*相当于数据库的连接池
	里面出现错误是看看hibernate.cfg.xml里是否出错，因为它的内容就在<session-factory>中
 	创建session对象，hibernate一级缓存由session缓存，单线程的对象，线程安全的问题，刷出缓存，更新缓存的数据session.flush,
	内部包含session.commit,session.flush能变更的查询，*/
	//从工厂中回去session对象，与数据库会话
	Session session = factory.openSession();
	// 开启事务
	Transaction tr = session.beginTransaction();
	// 编写保存代码
	Student c = new Student();
	//  已经自动递增
	//c.setId(0);
	c.setName("测试名称");
	c.setAge(110);
	// 保存客户
	//System.out.println("手动赋值为："+c.getId());
	//session.save(c);
	//session.get(arg0, arg1)arg0__javabean的对象，主键的值
	Student s=session.get(Student.class,1);
	System.out.println(s);
	//session.delete(c);
	//System.out.println("手动赋值为："+c.getId());
	// 提交事务
	tr.commit();
	// 释放资源
	session.close();
	factory.close();
	}
	/**
	 * 状态
	 *  瞬时  数据库中没有数据与之对应 超过作用域会被JVM垃圾回收器回收，
		持久
		托管
	 */

	@Test
	public void getStudent(Student stu){
	 	Session session=HibernateUtils.openSession();
	 	Transaction tr=session.beginTransaction();
	 	  Criteria criteria = session.createCriteria(Student.class);
	 	  // 设置排序
	 	  criteria.addOrder(Order.desc("id"));
	 	  criteria.setFirstResult(0);
	 	  criteria.setMaxResults(3);
	 	  List<Student> list = criteria.list();
	 	  for (Student Student : list) {
	 	      System.out.println(Student);
	 	  }
	 	  tr.commit();
	 	session.close();
	}

}

```
src目录下
## hibernate.cfg.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
<session-factory>
<!-- hibernate可以省略 -->
<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
<property name="hibernate.connection.url">jdbc:mysql://localhost:3306/mydata</property>
<property name="hibernate.connection.username">root</property>
<property name="hibernate.connection.password">123456</property>
<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
<!-- 以下为可选配置 -->
<!-- 显示sql语句 -->
<property name="hibernate.show_sql">true</property>
<!--  让sql语句格式化显示 -->
<property name="hibernate.format_sql">true</property>
<!-- 创建映射的表 慎用，否则表中的数据会被删掉，
<property name="hibernate.hbm2ddl.auto">create</property>-->
<!-- 没有表会创建，表不会删除,里面的数据还在，还可以进行字段的更新，在java的实例化对象中，添加它都在数据库中操作 但是不能删除-->
<property name="hibernate.hbm2ddl.auto">update</property>
<!-- 对映射进行校验 -->
<property name="hibernate.hbm2ddl.auto">validate</property>
<!-- 现在的Hibernate框架中，使用session对象开启事务，所以需要来传递session对象，框架提供了ThreadLocal的方式
    * 需要在hibernate.cfg.xml的配置文件中提供配置-->
    <!-- 这句话是为了getCurrentSession做准备
      a、如果是本地事务（jdbc事务）
     <propertyname="hibernate.current_session_context_class">thread</property>
      b、如果是全局事务（jta事务）
       <propertyname="hibernate.current_session_context_class">jta</property> -->
<property name="hibernate.current_session_context_class">thread</property>
<!-- 必须要有的映射路径 ***************************hibernate -->
<mapping resource="com/DB/edu/Student.hbm.xml"/>
</session-factory>
</hibernate-configuration>
```

## Student.hbm.xml
对数据的表的映射

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
"-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<!-- multable: 配置是否可更新  动态的更新， 对象为null时均不在updata的语句中 -->
<hibernate-mapping package="com.DB.edu">
<class name="com.DB.edu.Student" table="student">
 <id name="id" column="id"><!-- type种类 -->
<generator class="increment"/><!--注意点: 适用类型 （代理主键） 数据类型  数据库的限制   -->
</id>
<property name="name" column="name" />
<property name="age" column="age"/>
</class>
</hibernate-mapping>
```
在hiubernate中常用的两种查询方式参照
HQL查询
QBC（Query By Criteria）查询
原生SQL查询
https://blog.csdn.net/weixin_39941298/article/details/81699743
TestSelect .java测试类
```java
import java.util.List;

import org.hibernate.Query;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

import com.DB.edu.Emp;

public class TestSelect {
	public static void main(String[] args) {
		select();

	}

	public static void select(){
			// 先加载配置文件
			Configuration config = new Configuration().configure();
			SessionFactory factory = config.buildSessionFactory();
			Session session = factory.openSession();
			//Transaction tr=session.beginTransaction();
			String hql="from Emp where empmoney>?";
			Query query= session.createQuery(hql);
			query.setInteger(0, 5000);
			List<Emp> em=query.list();
			for(Emp e:em){
				System.out.println(e.toString());
			}
			//tr.commit();
			session.close();
			factory.close();
	}

}

```