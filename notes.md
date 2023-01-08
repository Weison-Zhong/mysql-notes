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
  * 