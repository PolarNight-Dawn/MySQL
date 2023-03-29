## 题目 + 解析

### 1.每一个部门的最高薪水人员名称

#### 思路

1.将emp表分组成每个部门

```sql
select deptno as sal from emp group by deptno;
```

2.找出最高薪资人员，显示其名称

```sql
select e2.ename from emp e2 inner join (select max(sal) as sal from emp group by deptno) e1 on e1.sal = e2.sal;
```

***key：把一张表看成两张表***

#### 反思

having子句中好像只能用条件查询

为毛`select ename,max(sal) from emp group by deptno; ename`字段显示的不是分组后的记录

### 2.高于每个部门的平均薪资的人

#### 思路

1.每个部门的平均薪资

```sql
select deptno, avg(sal) as sal from emp group by deptno;
```

2.高于所在部门平均薪资的人

```sql
select e1.ename from emp e1 inner join (select deptno,avg(sal) as sal from emp group by deptno) e2 on e1.deptno = e2.deptno and e1.sal > e2.sal; //连接查询

select e1.ename from emp e1, (select deptno, avg(sal) as sal from emp group by deptno) e2 where e1.deptno = e2.deptno and e1.sal > e2.sal; //子查询
```

***key：把一张表看成两张表***

#### 反思

这里没法使用自连接，它会将原表每一项与平均薪资表每一项比较，只要大于平均薪资表中的任意一项就会显示，显然不符合题意高于所在部门

补：我是个joker！可以使用，只要将连接查询的条件查询写在on子句中，并使用运算符

### 3.每一个部门平均薪水的等级

#### 思路

1.每个部门的平均薪资

```sql
select deptno, avg(sal) as sal from emp group by deptno;
```

2.平均薪水的等级

```sql
select e.deptno, s.grade from (select deptno, avg(sal) as sal from emp group by deptno) e inner join salgrade s on e.sal between s.losal and s.hisal;
```

***key：两表联查***

#### 反思

我真是个joker！`select e.deptno s.grade`多字段查询，用逗号隔开

### 4.不使用Max找出工资最高值

#### 思路

按薪资降序排序

```sql
select * from emp order by sal desc;
```

#### 反思

无

### 5.取得平均薪水最高部门的部门编号

#### 思路

1.每个部门的平均薪资

```sql
select deptno, avg(sal) as sal from emp group by deptno;
```

2.平均薪水最高部门的编号

```sql
select deptno, avg(sal) as sal from emp group by deptno order by sal desc limit 1;
```

#### 反思

无

### 6.取得平均薪水最高的部门的部门名称

#### 思路

1.每个部门的平均薪资

```sql
select deptno, avg(sal) as sal from emp group by deptno;
```

2.平均薪水最高的部门的部门名称

```sql
select e1.deptno, e2.sal from emp e1 inner join (select deptno, avg(sal) as sal from emp group by deptno) e2 on e1.deptno = e2.deptno order by sal desc limit 1;
```

***key：limit***

#### 反思

无

### 7.求平均薪水的等级最低的部门的部门名称

#### 思路

1.每个部门的平均薪资

```sql
select deptno, avg(sal) as sal from emp group by deptno;
```

2.平均薪水的等级最低的部门的部门名称

```sql
select e2.deptno, s.grade, e1.sal from salgrade s inner join (select deptno, avg(sal) as sal from emp group by deptno) e1 on e1.sal between s.losal and s.hisal inner join emp e2 on e1.deptno = e2.deptno order by sal limit 1;
```

***key：多表联查***

#### 反思

开始以为跟第6题是一样的，仔细读题后才发现需要显示的是两个字段：等级，部门名称（可以把薪资字段也显示出来），这俩个字段涉及了三张表：原表，平均薪资表，薪资等级表，所以采用多表联查，且需要注意的是有可能出现薪资等级相同的不同平均薪资

8.取得比普通员工（员工代码没有在mgr字段上出现的）的最高薪水还要高的领导人姓名

9、取得薪水最高的前五名员工

10、取得薪水最高的第六到第十名员工

11、取得最后入职的 5 名员工

12、取得每个薪水等级有多少员工

13、面试题：
有 3 个表 S(学生表)，C（课程表），SC（学生选课表）
S（SNO，SNAME）代表（学号，姓名）
C（CNO，CNAME，CTEACHER）代表（课号，课名，教师）
SC（SNO，CNO，SCGRADE）代表（学号，课号，成绩）
问题：
1，找出没选过“黎明”老师的所有学生姓名。
2，列出 2 门以上（含2 门）不及格学生姓名及平均成绩。
3，即学过 1 号课程又学过 2 号课所有学生的姓名。

14、列出所有员工及领导的姓名

15、列出受雇日期早于其直接上级的所有员工的编号,姓名,部门名称

16、 列出部门名称和这些部门的员工信息, 同时列出那些没有员工的部门

17、列出至少有 5 个员工的所有部门

18、列出薪金比"SMITH" 多的所有员工信息

19、 列出所有"CLERK"( 办事员) 的姓名及其部门名称, 部门的人数

20、列出最低薪金大于 1500 的各种工作及从事此工作的全部雇员人数

21、列出在部门"SALES"< 销售部> 工作的员工的姓名, 假定不知道销售部的部门编号

22、列出薪金高于公司平均薪金的所有员工, 所在部门, 上级领导, 雇员的工资等级

23、 列出与"SCOTT" 从事相同工作的所有员工及部门名称

24、列出薪金等于部门 30 中员工的薪金的其他员工的姓名和薪金

25、列出薪金高于在部门 30 工作的所有员工的薪金的员工姓名和薪金、部门名称

26、列出在每个部门工作的员工数量, 平均工资和平均服务期限

27、 列出所有员工的姓名、部门名称和工资

28、列出所有部门的详细信息和人数

29、列出各种工作的最低工资及从事此工作的雇员姓名

30、列出各个部门的 MANAGER( 领导) 的最低薪金

31、列出所有员工的 年工资, 按年薪从低到高排序

32、求出员工领导的薪水超过3000的员工名称与领导

33、求出部门名称中, 带'S'字符的部门员工的工资合计、部门人数

34、给任职日期超过 30 年的员工加薪 10%