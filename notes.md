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