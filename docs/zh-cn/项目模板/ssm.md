SSM框架中各层级间的作用及关系

> ​	表现层（springMVC）：

   Controller层（Handler层）调用业务层

>   业务层（Spring）

   Service层 接口 Impl实现类 调用持久层

>   持久层（Mybatis）

   Dao层：接口

>   Mapper层：（Mybatis的逆向工程）跟Dao层的接口匹配

负责具体的业务模块流程的控制Controller层通过要调用Service层的接口来控制业务流程，控制的 配置也在Spring配置文件里面。

Service层：负责业务模块的逻辑应用设计。

首先设计其接口，然后再实现他的实现类。

通过对Spring配置文件中配置其实现的关联，完成此步工作，我们 就可以通过调用Service的接口来进行业务处理。最后通过调用DAO层已定义的接口，去实现Service具体的 实现类。

持久层（Mybatis）：Dao层（Mapper层）

**Dao层：负责与数据库进行交互设计，用来处理数据的持久化工作。**

DAO层的设计首先是设计DAO的接口，然后在Spring的配置文件中定义此接口的实现类，就可在其他模块中 调用此接口来进行数据业务的处理，而不用关心接口的具体实现类是

哪个类，这里用到的就是反射机制， DAO层的数据源配置，以及有关数据库连接的参数都在Spring的配置文件中进行配置。

**视图层：View层**

负责前台jsp页面的展示。此层需要与Controller层结合起来开发。

各层间的联系：

本来Controller层与View层是可以放在.jsp文件里一起开发的，但是为了降低代码的复杂度，提高其可维护性，将其分为了这两层，这也体现了MVC框架的特性，即结构清晰，耦合度低。

Service层是建立在DAO层之上的，建立了DAO层后才可以建立Service层，而Service层又是在Controller层之下的，因而Service层应该既调用DAO层的接口，又要提供接口给Controller层的类来进行调用，它刚好处于一个中间层的位置。每个模型都有一个Service接口，每个接口分别封装各自的业务处理方法。

例子：实现数据库中数据的显示

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>WebSSM</display-name>
  
  <!-- 加载spring容器 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:ApplicationContext-*.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
  
  
  <!-- springmvc前端控制器 -->
  <servlet>
  	<servlet-name>springMvc</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>classpath:SpringMvc.xml</param-value>
  	</init-param>
  	<!-- 在tomcat启动的时候就加载这个servlet -->
  	<load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
  	<servlet-name>springMvc</servlet-name>
  	<url-pattern>*.action</url-pattern>
  </servlet-mapping>
  
  <!-- 配置Post请求乱码 -->
  <filter>
		<filter-name>CharacterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>utf-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>CharacterEncodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
  
</web-app>
```

 SpringMvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:dubbo="http://code.alibabatech.com/schema/dubbo" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
        http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
    
    <!-- @Controller注解扫描 -->
    <context:component-scan base-package="com.edu.Controller"></context:component-scan>
    <!-- 注解驱动:
    
    		替我们显示的配置了最新版的注解的处理器映射器和处理器适配器 -->
    <mvc:annotation-driven/>
    
    <!-- 配置视图解析器 
	作用:在controller中指定页面路径的时候就不用写页面的完整路径名称了,可以直接写页面去掉扩展名的名称
	-->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<!-- 真正的页面路径 =  前缀 + 去掉后缀名的页面名称 + 后缀 -->
		<!-- 前缀 -->
		<property name="prefix" value="/WEB-INF/jsp/"></property>
		<!-- 后缀 -->
		<property name="suffix" value=".jsp"></property>
	</bean>
	
	<!-- 配置自定义转换器 
	注意: 一定要将自定义的转换器配置到注解驱动上
	
	<bean id="conversionService"
		class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
		<property name="converters">
			<set>
				 指定自定义转换器的全路径名称 
				<bean class="com.edu.Controller.converter.CustomGlobalStrToDateConverter"/>
			</set>
		</property>
	</bean>-->
	
</beans>
```
ApplicationContext-trans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd 
http://www.springframework.org/schema/tx 
http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 事务管理器 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>
	
	<!-- 通知 就是增强transactionManager-->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 传播行为 -->
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="insert*" propagation="REQUIRED" />
			<tx:method name="delete*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
			<tx:method name="find*" propagation="SUPPORTS" read-only="true" />
			<tx:method name="get*" propagation="SUPPORTS" read-only="true" />
		</tx:attributes>
	</tx:advice>
	
	<!-- 切面 -->
	<aop:config>
	<!-- 
	使用 自己写的额就要用aop:aspect
	使用spring的事务使用aop:advisor 
	-->
		<aop:advisor advice-ref="txAdvice"
			pointcut="execution(* com.edu.ServiceImp.*.*(..))" />
	</aop:config>
	
</beans>
```
ApplicationContext-Service.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd 
http://www.springframework.org/schema/tx 
http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- @Service扫描 -->
	<context:component-scan base-package="com.edu.ServiceImp"></context:component-scan>
</beans>
```
ApplicationContext-Dao.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 加载配置文件 -->
	<context:property-placeholder location="classpath:db.properties" />
	<!-- 数据库连接池 -->
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<property name="maxActive" value="10" />
		<property name="maxIdle" value="5" />
	</bean>
	
	<!-- mapper配置 -->
	<!-- 让spring管理sqlsessionfactory 使用mybatis和spring整合包中的 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 数据库连接池 -->
		<property name="dataSource" ref="dataSource" />
		<!-- 加载mybatis的全局配置文件 -->
		<property name="configLocation" value="classpath:SqlMapConfig.xml" />
	</bean>
	
	<!-- 配置Mapper扫描器扫描包中的所有map -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.edu.Dao"/>
		
	</bean>

</beans>

```
SqlMapConfig.xml,在这里必须要有，可以为空，在这里可以配置别名
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	
</configuration>

```
db.properties
```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/springmvc?characterEncoding=utf-8
jdbc.username=root
jdbc.password=123456
```

一个简单的SSM框架的配置文件在这里了，然后就是代码的扫描与调用。