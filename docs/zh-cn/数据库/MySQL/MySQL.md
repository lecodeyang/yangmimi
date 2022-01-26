###  1. 创建表（CREATE TABLE ）

1、表名的 命名规则：
  表名首字母应该为字母
   不能使用 MySQL 保留字为表名
   表名最大长度为 128 个字符
   同一数据库中的不同表不能具有相同的名称

​	可以使用下划线、数字、字母、@、#和$符号组成，其中字母可以是 26 个英文字母或其他语言的字母字符，但不能使用空格和其他特殊字符
2、MySQL 数据库中 常用的数据类型，这里讲的有表格清楚
http://www.cnblogs.com/pingfan21/p/9124208.html

3、MySQL 数据库中 表的完整性约束

> PRIMARY KEY、FOREIGN KEY、UNIQUE、NOT NULL、DEFAULT

4、创建语法：
**CREATE TABLE [IF NOT EXISTS] **<表名>
(<字段名 1><类型(长度)> [字段级约束],
<字段名 2><类型(长度)> [字段级约束],
…
<字段名 n><类型(长度)> [字段级约束]
[表级约束]
)

#### 注意事项：

​	l  建表顺序：**先建主表，再建子表，删除则反之。**
​	l 非空和默认约束只能设成字段级的
​	l 若主键由多个属性组成， 主键约束要定义成表级的，格式为：
**PRIMARY KEY(主键)**
​	l  外键约束要定义在下面，格式为：

**FOREIGN KEY(外键) REFERENCES 父表名(被参照的字段名)**

 ① 父表和子表必须使用相同的存储引擎，存储引擎只能为 InnoDB,而且禁止
使用临时表
② 外键列和参照列必须具有相似的数据类型，其中数字的长度或是否有符号
位必须相同，而字符的长度则可以不同(有的 DBMS 要求完全一样的数据
类型)，建议外键列和参照列数据类型完全一致。
③ 外键约束的参数说明、

**CASCADE:**

​				从父表删除或更新记录时，自动删除或更新子表中匹配的行
 **SET NULL:** 

​				从父表删除或更新行，并设置子表中的外键列为 NULL。
​				如果使用该选项，必须保证子表中该列没有指定 NOT NULL
 **RESTRICT:**

拒绝对父表的删除或更新操作（不定义 ON DELETE 和 ON
UPDATE 时这是默认设置，）
 **NO ACTION**:什么也不做
	l  设表的字段值自动增加（**AUTO_INCREMENT** ）
	① 该字段必须是整数类型
	② 必须设该字段为主键



## 2. 基本操作

一、插入数据 一、插入数据
1、单行插入命令

```sql
(1) INSERT INTO <表名>[(字段列表)] VALUES(值 1，值 2……)
(2) INSERT INTO <表名> SET 字段名 1＝值 1[,字段名 2＝值 2……]
```

注意：
² 先插入父表，再插入子表
² 数值列表中字符、日期型的数据要加单引号；
² 若不插入所有列的值，则必须 insert into 表名(字段列表)
² 自动增长列写成 NULL 或 default 都可以
² 默认列可以写成 default
2、多行插入命令

```sql
INSERT INTO <表名>[(字段列表)]
VALUES(值 1，值 2……)，
……
(值 1，值 2……)
```

（2）INSERT INTO<表名>[(字段列表)] SELECT 子句 （注：该语句用于从一个表向另一个表值复制多行记录）
3、创建表并复制数据

```sql
CREATE TABLE <表名> AS SELECT 子句
```

（注：也可以只复制表的结构，就是要把查询条件设为永远为“假”）
例 1：把 stuinfo 所有女生的记录另存 为新表 stuinfo_female

```sql
CREATE TABLE stuinfo_female AS SELECT * FROM stuinfo WHERE stusex='女'
```

例 2：创建一个新表 stuinfo2, 字段定义与 stuinfo 表一致

```sql
CREATE TABLE stuinfo_female AS SELECT * FROM stuinfo WHERE 1＝2
```

二、修改数据 二、修改数据
（1） 直接修改

```sql
UPDATE <表名>
SET <字段名>＝<表达式 1>[，<字段名 2>＝<表达式 2>…]
[WHERE <条件>]
```

（2） 嵌套修改

```sql
UPDATE <表名>
SET <字段名>＝<返回单个值的子查询>
[WHERE <条件>]
```

3 、删除数据
（1）删除所选行

```sql
DELETE FROM <表名> [WHERE<条件>]
```

注意：先删除子表相关记录，再删除父表记录!
（2）删除整表数据

```sql
TRUNCATE TABLE <表名>
```

**注意：DELETE 与 TRUNCATE 语句的区别**
a) DELETE 语句是 DML 语句，TRUNCATE 语句通常被认为是 DDL 语句
b) 使用 DELETE 语句时，每删除一条记录都会在日志中记录，而使用 TRUNCATE 语句时，不会在日志中记录删除的内容，因此 TRUNCATE 语
句的执行效率比 DELETE 语句高。
c) DELETE 语句后面可以跟 WHERE 子句，通过指定 WHERE 子句中的条件表达式只删除满足条件的部分记录，而 TRUNCATE 语句只能用于删
除表中的所有记录
d) 使用 TRUNCATE 语句删除表中的数据，再向表中添加记录时，自动增加字段的默认初始值重新由 1 开始，使用 DELETE 语句删除表中所有记
录，再向表中添加记录时，自动增加字段的值为删除时该字段的最大值加 1。

## 3. 例子：

```sql
CREATE TABLE dept (
	deptno CHAR (2) PRIMARY KEY,
	dname VARCHAR (14) UNIQUE,
	loc VARCHAR (50)
);

CREATE TABLE if not EXISTS emp (
	empno CHAR (4) PRIMARY KEY,
	ename VARCHAR (10),
	job VARCHAR (9),
	mgr CHAR (4),
	hiredate DATE,
	sal DECIMAL (7, 2),
	deptno char(2),
	FOREIGN key (deptno)REFERENCES dept (deptNo)
);

给cmp表增加一列comm，其数据类型及精度为DECIMAL(7,2)
alter table emp add comm DECIMAL(7,2);

给dept表插入一行数据 部门编号为60，部门名称为SCHOOL,地址为NanJing
INSERT INTO dept VALUES ('60','SCHOOL','NanJing');

把员工编号为‘7788’的员工月薪增加1000
UPDATE emp SET sal=sal+1000 WHERE empno='7788';

删除部门编号为30的所有员工的记录
DELETE from emp WHERE deptno='30';

列出职位为MANAGER 的员工的编号，姓名
SELECT empno,ename FROM emp WHERE job='MANAGER';

查询至少有一个员工的部门编号
SELECT DISTINCT deptno from emp;

找出部门10中的经理MANAGER和部门20中的普通员工CLERK
SELECT * FROM emp WHERE (deptno='10' and job='MANAGER')or(deptno='20' and job='CLERK');

显示雇员姓名，根据其服务年限，将最老的排在最前面
SELECT ename from emp ORDER BY hiredate;

统计平均工资大于500的各部门的平均工资
SELECT deptno,AVG(sal) FROM emp WHERE sal>500 GROUP BY deptno;

列出所有员工的姓名，该员工所在的部门名称和工资
SELECT ename,dname,sal FROM emp join dept on emp.deptno=dept.deptno;

列出受雇日期早于其直接上级上午所有员工， 连接查询
SELECT e1.* FROM emp e1 JOIN emp e2 ON e1.mgr=e2.empno WHERE e1.hiredate<e2.hiredate;

列出至少有一个员工的部门信息 子查询
SELECT * FROM dept WHERE deptno IN(SELECT DISTINCT deptno FROM emp);
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/20190322173511563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwMTgzMjgx,size_16,color_FFFFFF,t_70)
三张表结构

```sql
查询学号为S001的学生的课程

SELECT cname from stumarks c, stucourse m WHERE c.cno=m.cno and stuno='S001'


select * from stuinfo ORDER BY stubirth


//倒序排列查出来的成绩 按照stuscore分组后面加条件 
SELECT * from stumarks GROUP BY stuscore having stuscore>86 ORDER BY  stuscore desc
//正序
SELECT * from stumarks GROUP BY stuscore having stuscore>86 ORDER BY  stuscore ASC

```

以下是方便自己的查看
```sql
SELECT *
FROM stuinfo join stumarks on stuinfo.stuno=stumarks.stuno;

SELECT *
FROM stuinfo,stumarks
WHERE stuinfo.stuno=stumarks.stuno

SELECT *
FROM stuinfo LEFT JOIN stumarks on stuinfo.stuno=stumarks.stuno

SELECT stumarks RIGHT  JOIN stuinfo on stuinfo.stuno=stumarks.stuno

SELECT stusex,AVG(stuscore)
from stuinfo,stumarks
WHERE stuinfo.stuno=stumarks.stuno
GROUP BY stusex

SELECT stuinfo.stuno,stuname,cname
FROM stuinfo,stumarks,stucourse
WHERE stuinfo.stuno=stumarks.stuno AND stumarks.cno=stucourse.cno

SELECT i.stuno,stuname,cname,stuscore
FROM stuinfo i JOIN stumarks m on i.stuno=m.stuno
JOIN stucourse c on c.cno=m.cno

SELECT i.stuno,stuname,cname
FROM stuinfo i,stucourse c,stumarks m
WHERE i.stuno=m.stuno and c.cno=m.cno and stuname like '刘%'

SELECT stuno,stuscore
FROM stucourse c,stumarks m
WHERE c.cno=m.cno and cname='英语' AND stuscore BETWEEN 80 and 90 

SELECT AVG(YEAR(SYSDATE())-YEAR(stubirth))
FROM stuinfo i,stumarks m
where i.stuno=m.stuno
AND cno='0004'

SELECT AVG(year(SYSDATE())-year(stubirth))
FROM stuinfo JOIN stumarks ON stuinfo.stuno=stumarks.stuno
WHERE cno='0004'
```

