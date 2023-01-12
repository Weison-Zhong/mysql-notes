### 1 SQL分类
  * DQL：数据查询语言（凡是带有select关键字的都是查询语句）
  * DML：数据操作语言（凡是对表当中的数据进行增删改的都是DML）
  * DDL：数据定义语言(操作的是表的结构create,drop,alter,drop,alter)
  * TCL：事务控制语言
  * DCL：数据控制语言(授权grant、撤销权限revoke)

### 2 导入数据
  1. create database test1;
  2. use test1;
  3.  source D:\learn\SQL\mysql-notes\document\test1.sql(不可有中文)

### 3 DQL
#### 3.1 简单查询
  * 查一个字段
    * 查询所有部门名字  select dname from dept;
  * 查多个字段
    * 查询所有部门编号和部门名字  select deptno,dname from dept;
  * 查询所有字段: * 或 把所有字段写上(推荐)
  * 起别名(只改变查询结果的显示，不改变原表)
    * select deptno,dname as deptname from dept; //as
    * select deptno,dname deptname from dept;  //空格
    * select deptno,dname 'dept name' from dept;  //当别名有空格使用单双引号包住(建议单引号，Oracle不支持双引号)
  * 查询员工年薪
    * select ename,sal*12 from emp; // *12 字段可以使用数学表达式
    * select ename,sal*12 as '年薪' from emp;
  
#### 3.2 条件查询(筛选)
  * =,!=等同于<>,<,>,<=,>=
    * 查询薪资等于800的员工姓名和编号  select empno,ename from emp where sal = 800;
  * between … and …. 等同于 >= and <= // 区间
    * select ename,sal from emp where sal between 800 and 5000;
  * is null,is not null 不为空
  * and 优先级比or高，可通过()改变优先级
  * or 
    * select ename,sal from emp where sal = 800 or sal = 5000;
  * in,not in  // in后是具体的值，等同多个or
    *  select ename,sal from emp where sal in (800,5000);
    *  select ename,sal from emp where sal in (800,3000,5000);s
  * like 模糊查询,%匹配任意多个字符，_匹配任意一个字符
    * 找出名字有o的 select ename from emp where ename like '%o%';
    * 找出名字以K开头 select ename from emp where ename like 'K%';
    * 找出名字第二个字母是A  select ename from emp where ename like '_A%';
    * 找出名字有_的 select ename form emp where ename like '%\_%';

 #### 3.3 排序
  * 升序 select ename,sal from emp order by sal; 默认升序
  * 升序 select ename,sal from emp order by sal asc; 指定升序
  * 降序 select ename,sal from emp order by sal desc;
  * 多字段排序 
    * 按薪资升序，若薪资相同按名字升序 select ename,sal from emp order by sal asc,ename asc;
   
#### 3.4 数据处理函数(单行处理函数,一个输入对应一个输出)
  * lower 转小写
    * select lower(ename) from emp;
    * select lower(ename) as ename from emp;
  * upper 转大写
  * substr 截取字符串 substr(被截取字段名，开始下标从1开始，截取的长度)
    *  select substr(ename,1,1) as ename from emp;
    * 找出名字以A开头除了 like 还有 select ename from emp where substr(ename,1,1) = 'A';
  * length
  * trim
  * round 四舍五入
  * rand 生成随机数
    * 100以内随机数 select round(rand()*100,0) from emp;
  * ifnull 可将null转换为一个具体值,数据库中只要有null参与的数学运算结果都是null
    * select ename,sal + comm as salcomm from emp;
    * 计算每个员工年薪 select ename,(sal + ifnull(comm,0))*12 as yearsal from emp;
  * case...when...
    * 当job是manger，工资加10%,当job是salseman，工资加50%  select ename,job,(case job when 'MANAGER' then sal * 1.1 when 'SALESMAN' then sal * 1.5 else sal end) as newsal from emp;
  
#### 3.5 分组函数(多行处理函数) 必须先分组(默认整张表为一组)后再用
 * count 计数
   *  select count(ename) from emp;
 * sum
   * 计算工资和 select sum(sal) from emp;
 * avg
 * max
   * 找出最高工资  select max(sal) from emp;
 * min
 * distinct 去重
  * select distinct job from emp;
 * 注意事项
   * 分组函数自动忽略null
   * count(*)和count(具体字段)区别是 * 统计所有行数，因为不存在所有列都是null，具体字段的话该字段为null不计数
   * 分组函数不可直接使用在where子句中,如select ename,sal from emp where sal > min(sal);

#### 3.6 分组查询
 * 计算各岗位工资和(按job分组，然后求和)
   *  select job,sum(all) from emp group by job;
 * 找出每个部门的最高薪资
   * select deptno,max(sal)  from emp group by deptno;
 * 找出每个部门，不同岗位的最高薪资(两个字段联合分组)
   *  select deptno,job,max(sal) from emp group by deptno,job;
 * 找出每个部门最高薪资，要求显示最高薪资大于3000的，使用having对数据惊醒过滤
   * select deptno,max(sal) from emp group by deptno;
   * select deptno,max(sal) from emp group by deptno having max(sal) > 3000; 效率相对低
   * select deptno,max(sal) from emp where sal > 3000 group by deptno; 效率相对高
  * 找出每个部门平均薪资，要求显示平均薪资大于2500，此时where无法实现，只能用having
    * select deptno,avg(sal) from emp group by deptno;
    * select deptno,avg(sal) from emp group by deptno having avg(sal) > 2500;

#### 3.7 连接查询
##### 3.7.1 内连接之等值连接
* SELECT ename,dname from emp,dept WHERE emp.deptno = dept.deptno;
* SELECT e.ename,d.dname from emp e,dept d WHERE e.deptno = d.deptno; // 表起别名，相比上面的效率更高
* SELECT e.ename,d.dname from emp e join dept d on e.deptno = d.deptno; //99语法
##### 3.7.2 内连接之非等值连接
* SELECT e.ename,e.sal,s.grade from emp e join sal s on e.sal BETWEEN s.losal and s.hisal; // 找出每个员工的薪资等级，要求显示员工姓名、薪资、薪资等级

##### 3.7.3 内连接之自连接(一张表看作两张表)
* SELECT a.ename as '员工名',b.ename as '领导名' FROM emp a join emp b on a.mgr = b.EMPNO; // 找出员工的领导，显示员工名与领导名

##### 3.7.4 外连接

* mysql> select * from emp; e
```
+-------+--------+-----------+------+------------+---------+---------+--------+
| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
+-------+--------+-----------+------+------------+---------+---------+--------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     20 |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450.00 |    NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000.00 |    NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     20 |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300.00 |    NULL |     10 |
+-------+--------+-----------+------+------------+---------+---------+--------+
```
* mysql> select * from dept; d
```
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
```

* 内连接：（A和B连接，AB两张表没有主次关系。平等的。）
```
select 
	e.ename,d.dname
from
	emp e
join
	dept d
on
	e.deptno = d.deptno; //内连接的特点：完成能够匹配上这个条件的数据查询出来。

+--------+------------+
| ename  | dname      |
+--------+------------+
| CLARK  | ACCOUNTING |
| KING   | ACCOUNTING |
| MILLER | ACCOUNTING |
| SMITH  | RESEARCH   |
| JONES  | RESEARCH   |
| SCOTT  | RESEARCH   |
| ADAMS  | RESEARCH   |
| FORD   | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| TURNER | SALES      |
| JAMES  | SALES      |
+--------+------------+
```

* 外连接（右外连接）：
```
select 
	e.ename,d.dname
from
	emp e 
right join 
	dept d
on
	e.deptno = d.deptno;

// outer是可以省略的，带着可读性强。
select 
	e.ename,d.dname
from
	emp e 
right outer join 
	dept d
on
	e.deptno = d.deptno;
```
* right代表什么：表示将join关键字右边的这张表看成主表，主要是为了将
这张表的数据全部查询出来，捎带着关联查询左边的表。
在外连接当中，两张表连接，产生了主次关系。

* 外连接（左外连接）：
```
select 
	e.ename,d.dname
from
	dept d 
left join 
	emp e
on
	e.deptno = d.deptno;

// outer是可以省略的，带着可读性强。
select 
	e.ename,d.dname
from
	dept d 
left outer join 
	emp e
on
	e.deptno = d.deptno;
```
* 带有right的是右外连接，又叫做右连接。
* 带有left的是左外连接，又叫做左连接。
* 任何一个右连接都有左连接的写法。
* 任何一个左连接都有右连接的写法。
```
+--------+------------+
| ename  | dname      |
+--------+------------+
| CLARK  | ACCOUNTING |
| KING   | ACCOUNTING |
| MILLER | ACCOUNTING |
| SMITH  | RESEARCH   |
| JONES  | RESEARCH   |
| SCOTT  | RESEARCH   |
| ADAMS  | RESEARCH   |
| FORD   | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| TURNER | SALES      |
| JAMES  | SALES      |
| NULL   | OPERATIONS |
+--------+------------+
```

* 案例：查询每个员工的上级领导，要求显示所有员工的名字和领导名？
```
	select 
		a.ename as '员工名', b.ename as '领导名'
	from
		emp a
	left join
		emp b
	on
		a.mgr = b.empno; 
	
	+--------+--------+
	| 员工名      | 领导名     |
	+--------+--------+
	| SMITH  | FORD   |
	| ALLEN  | BLAKE  |
	| WARD   | BLAKE  |
	| JONES  | KING   |
	| MARTIN | BLAKE  |
	| BLAKE  | KING   |
	| CLARK  | KING   |
	| SCOTT  | JONES  |
	| KING   | NULL   |
	| TURNER | BLAKE  |
	| ADAMS  | SCOTT  |
	| JAMES  | BLAKE  |
	| FORD   | JONES  |
	| MILLER | CLARK  |
	+--------+--------+
```

* 2.9、三张表，四张表怎么连接？
```
	语法：
		select 
			...
		from
			a
		join
			b
		on
			a和b的连接条件
		join
			c
		on
			a和c的连接条件
		right join
			d
		on
			a和d的连接条件
		
		一条SQL中内连接和外连接可以混合。都可以出现！

	案例：找出每个员工的部门名称以及工资等级，
	要求显示员工名、部门名、薪资、薪资等级？
	
	select 
		e.ename,e.sal,d.dname,s.grade
	from
		emp e
	join
		dept d
	on 
		e.deptno = d.deptno
	join
		salgrade s
	on
		e.sal between s.losal and s.hisal;
	
	+--------+---------+------------+-------+
	| ename  | sal     | dname      | grade |
	+--------+---------+------------+-------+
	| SMITH  |  800.00 | RESEARCH   |     1 |
	| ALLEN  | 1600.00 | SALES      |     3 |
	| WARD   | 1250.00 | SALES      |     2 |
	| JONES  | 2975.00 | RESEARCH   |     4 |
	| MARTIN | 1250.00 | SALES      |     2 |
	| BLAKE  | 2850.00 | SALES      |     4 |
	| CLARK  | 2450.00 | ACCOUNTING |     4 |
	| SCOTT  | 3000.00 | RESEARCH   |     4 |
	| KING   | 5000.00 | ACCOUNTING |     5 |
	| TURNER | 1500.00 | SALES      |     3 |
	| ADAMS  | 1100.00 | RESEARCH   |     1 |
	| JAMES  |  950.00 | SALES      |     1 |
	| FORD   | 3000.00 | RESEARCH   |     4 |
	| MILLER | 1300.00 | ACCOUNTING |     2 |
	+--------+---------+------------+-------+
```
* 案例：找出每个员工的部门名称以及工资等级，还有上级领导，
	要求显示员工名、领导名、部门名、薪资、薪资等级？
```
	select 
		e.ename,e.sal,d.dname,s.grade,l.ename
	from
		emp e
	join
		dept d
	on 
		e.deptno = d.deptno
	join
		salgrade s
	on
		e.sal between s.losal and s.hisal
	left join
		emp l
	on
		e.mgr = l.empno;
	
	+--------+---------+------------+-------+-------+
	| ename  | sal     | dname      | grade | ename |
	+--------+---------+------------+-------+-------+
	| SMITH  |  800.00 | RESEARCH   |     1 | FORD  |
	| ALLEN  | 1600.00 | SALES      |     3 | BLAKE |
	| WARD   | 1250.00 | SALES      |     2 | BLAKE |
	| JONES  | 2975.00 | RESEARCH   |     4 | KING  |
	| MARTIN | 1250.00 | SALES      |     2 | BLAKE |
	| BLAKE  | 2850.00 | SALES      |     4 | KING  |
	| CLARK  | 2450.00 | ACCOUNTING |     4 | KING  |
	| SCOTT  | 3000.00 | RESEARCH   |     4 | JONES |
	| KING   | 5000.00 | ACCOUNTING |     5 | NULL  |
	| TURNER | 1500.00 | SALES      |     3 | BLAKE |
	| ADAMS  | 1100.00 | RESEARCH   |     1 | SCOTT |
	| JAMES  |  950.00 | SALES      |     1 | BLAKE |
	| FORD   | 3000.00 | RESEARCH   |     4 | JONES |
	| MILLER | 1300.00 | ACCOUNTING |     2 | CLARK |
	+--------+---------+------------+-------+-------+
```
* 3、子查询？

  * 3.1、什么是子查询？
	select语句中嵌套select语句，被嵌套的select语句称为子查询。

  * 3.2、子查询都可以出现在哪里呢？
```	
  select
		..(select).
	from
		..(select).
	where
		..(select).
```
* 3.3、where子句中的子查询
```
	案例：找出比最低工资高的员工姓名和工资？
		select 
			ename,sal
		from
			emp 
		where
			sal > min(sal);

		ERROR 1111 (HY000): Invalid use of group function
		where子句中不能直接使用分组函数。
	
	实现思路：
		第一步：查询最低工资是多少
			select min(sal) from emp;
			+----------+
			| min(sal) |
			+----------+
			|   800.00 |
			+----------+
		第二步：找出>800的
			select ename,sal from emp where sal > 800;
		
		第三步：合并
			select ename,sal from emp where sal > (select min(sal) from emp);
			+--------+---------+
			| ename  | sal     |
			+--------+---------+
			| ALLEN  | 1600.00 |
			| WARD   | 1250.00 |
			| JONES  | 2975.00 |
			| MARTIN | 1250.00 |
			| BLAKE  | 2850.00 |
			| CLARK  | 2450.00 |
			| SCOTT  | 3000.00 |
			| KING   | 5000.00 |
			| TURNER | 1500.00 |
			| ADAMS  | 1100.00 |
			| JAMES  |  950.00 |
			| FORD   | 3000.00 |
			| MILLER | 1300.00 |
			+--------+---------+
```

* 3.4、from子句中的子查询,注意：from后面的子查询，可以将子查询的查询结果当做一张临时表。（技巧）
```
	案例：找出每个岗位的平均工资的薪资等级。

	第一步：找出每个岗位的平均工资（按照岗位分组求平均值）
		select job,avg(sal) from emp group by job;
		+-----------+-------------+
		| job       | avgsal      |
		+-----------+-------------+
		| ANALYST   | 3000.000000 |
		| CLERK     | 1037.500000 |
		| MANAGER   | 2758.333333 |
		| PRESIDENT | 5000.000000 |
		| SALESMAN  | 1400.000000 |
		+-----------+-------------+t表

	第二步：克服心理障碍，把以上的查询结果就当做一张真实存在的表t。
	mysql> select * from salgrade; s表
	+-------+-------+-------+
	| GRADE | LOSAL | HISAL |
	+-------+-------+-------+
	|     1 |   700 |  1200 |
	|     2 |  1201 |  1400 |
	|     3 |  1401 |  2000 |
	|     4 |  2001 |  3000 |
	|     5 |  3001 |  9999 |
	+-------+-------+-------+
	t表和s表进行表连接，条件：t表avg(sal) between s.losal and s.hisal;
		
		select 
			t.*, s.grade
		from
			(select job,avg(sal) as avgsal from emp group by job) t
		join
			salgrade s
		on
			t.avgsal between s.losal and s.hisal;
		
		+-----------+-------------+-------+
		| job       | avgsal      | grade |
		+-----------+-------------+-------+
		| CLERK     | 1037.500000 |     1 |
		| SALESMAN  | 1400.000000 |     2 |
		| ANALYST   | 3000.000000 |     4 |
		| MANAGER   | 2758.333333 |     4 |
		| PRESIDENT | 5000.000000 |     5 |
		+-----------+-------------+-------+
```
* 3.5、select后面出现的子查询（这个内容不需要掌握，了解即可！！！）
```
案例：找出每个员工的部门名称，要求显示员工名，部门名？
	select 
		e.ename,e.deptno,(select d.dname from dept d where e.deptno = d.deptno) as dname 
	from 
		emp e;


	+--------+--------+------------+
	| ename  | deptno | dname      |
	+--------+--------+------------+
	| SMITH  |     20 | RESEARCH   |
	| ALLEN  |     30 | SALES      |
	| WARD   |     30 | SALES      |
	| JONES  |     20 | RESEARCH   |
	| MARTIN |     30 | SALES      |
	| BLAKE  |     30 | SALES      |
	| CLARK  |     10 | ACCOUNTING |
	| SCOTT  |     20 | RESEARCH   |
	| KING   |     10 | ACCOUNTING |
	| TURNER |     30 | SALES      |
	| ADAMS  |     20 | RESEARCH   |
	| JAMES  |     30 | SALES      |
	| FORD   |     20 | RESEARCH   |
	| MILLER |     10 | ACCOUNTING |
	+--------+--------+------------+

	//错误：ERROR 1242 (21000): Subquery returns more than 1 row
	select 
		e.ename,e.deptno,(select dname from dept) as dname
	from
		emp e;
	
	注意：对于select后面的子查询来说，这个子查询只能一次返回1条结果，
	多于1条，就报错了。！
```
* 4、union合并查询结果集
```
案例：查询工作岗位是MANAGER和SALESMAN的员工？
	select ename,job from emp where job = 'MANAGER' or job = 'SALESMAN';
	select ename,job from emp where job in('MANAGER','SALESMAN');
	+--------+----------+
	| ename  | job      |
	+--------+----------+
	| ALLEN  | SALESMAN |
	| WARD   | SALESMAN |
	| JONES  | MANAGER  |
	| MARTIN | SALESMAN |
	| BLAKE  | MANAGER  |
	| CLARK  | MANAGER  |
	| TURNER | SALESMAN |
	+--------+----------+
	
	select ename,job from emp where job = 'MANAGER'
	union
	select ename,job from emp where job = 'SALESMAN';
	
	+--------+----------+
	| ename  | job      |
	+--------+----------+
	| JONES  | MANAGER  |
	| BLAKE  | MANAGER  |
	| CLARK  | MANAGER  |
	| ALLEN  | SALESMAN |
	| WARD   | SALESMAN |
	| MARTIN | SALESMAN |
	| TURNER | SALESMAN |
	+--------+----------+

	union的效率要高一些。对于表连接来说，每连接一次新表，
	则匹配的次数满足笛卡尔积，成倍的翻。。。
	但是union可以减少匹配的次数。在减少匹配次数的情况下，
	还可以完成两个结果集的拼接。

	a 连接 b 连接 c
	a 10条记录
	b 10条记录
	c 10条记录
	匹配次数是：1000

	a 连接 b一个结果：10 * 10 --> 100次
	a 连接 c一个结果：10 * 10 --> 100次
	使用union的话是：100次 + 100次 = 200次。（union把乘法变成了加法运算）

union在使用的时候有注意事项吗？

	//错误的：union在进行结果集合并的时候，要求两个结果集的列数相同。
	select ename,job from emp where job = 'MANAGER'
	union
	select ename from emp where job = 'SALESMAN';

	// MYSQL可以，oracle语法严格 ，不可以，报错。要求：结果集合并时列和列的数据类型也要一致。
	select ename,job from emp where job = 'MANAGER'
	union
	select ename,sal from emp where job = 'SALESMAN';
	+--------+---------+
	| ename  | job     |
	+--------+---------+
	| JONES  | MANAGER |
	| BLAKE  | MANAGER |
	| CLARK  | MANAGER |
	| ALLEN  | 1600    |
	| WARD   | 1250    |
	| MARTIN | 1250    |
	| TURNER | 1500    |
	+--------+---------+
  ```