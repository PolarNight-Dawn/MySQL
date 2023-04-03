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
select e2.deptno, s.grade, e1.sal from salgrade s inner join (select deptno, avg(sal) as sal from emp group by deptno) e1 on e1.sal between s.losal and s.hisal inner join emp e2 on e1.deptno = e2.deptno order by sal limit 1; //没有考虑出现薪资等级相同的不同平均薪资

select e.deptno, min(e.grade) as grade from (select * from (select e1.deptno, s.grade, e1.sal from (select deptno, avg(sal) as sal from emp group by deptno order by sal) e1 inner join salgrade s on e1.sal between s.losal and s.hisal) e2) e group by e.grade; //出现薪资等级相同的不同平均薪资
```

***key：多表联查***

#### 反思

开始以为跟第6题是一样的，仔细读题后才发现需要显示的是两个字段：等级，部门名称（可以把薪资字段也显示出来），这俩个字段涉及了三张表：原表，平均薪资表，薪资等级表，所以采用多表联查，且需要注意的是有可能出现薪资等级相同的不同平均薪资

### 8.取得比普通员工（员工代码没有在mgr字段上出现的）的最高薪水还要高的领导人姓名

#### 思路

1.查询出‘领导人’表

```sql
select distinct e2.empno, e2.ename, e2.mgr, e2.sal from emp e1 inner join emp e2 on e1.mgr = e2.empno;
```

2.查询出‘领导人薪资比下级高’表

```sql
select distinct e3.ename from (select distinct e2.empno, e2.ename, e2.mgr, e2.sal from emp e1 inner join emp e2 on e1.mgr = e2.empno) e3 inner join emp e4 on e3.empno = e4.mgr and e3.sal > e4.sal;
```

***key：思路***

#### 反思

这道题困扰了我很久，最后发现思路有问题，也有可能是我太菜了实现不了最初的思路。最开始我想的是先查询出‘普通员工’表，然后和‘领导人’表，两表联查。但是对于‘普通员工’表的查询，我抠破脑壳都实现不了，我的想法是先查出‘领导人’表，然后与原表两表联查，使原表显示出没有领导人的普通员工。。。失败

### 9、取得薪水最高的前五名员工

#### 思路

1.按薪水降序排序

```sql
select e.ename, e.sal from emp e order by e.sal desc;
```

***key：order语句与limit***

2.取前5

```sql
select e.ename, e.sal from emp e order by e.sal desc limit 5;
```

#### 反思

无

### 10、取得薪水最高的第六到第十名员工

#### 思路

1.按薪水降序排序

```sql
select e.ename, e.sal from emp e order by e.sal desc;
```

2.取第6到第10

```sql
select e.ename, e.sal from emp e order by e.sal desc limit 5,5;
```

***key：order语句与limit***

#### 反思

无

### 11、取得最后入职的 5 名员工

#### 思路

1.按入职时间降序排序

```sql
select e.ename, e.hiredate from emp e order by e.hiredate desc;
```

2.取前5

```sql
select e.ename, e.hiredate from emp e order by e.hiredate desc limit 5;
```

***key：order语句与limit***

#### 反思

无

### 12、取得每个薪水等级有多少员工

#### 思路

1.查询出‘员工薪水等级’表

```sql
select e.ename, s.grade from emp e, salgrade s where e.sal between s.losal and s.hisal;
```

2.统计每个薪水等级的员工数量

```sql
select count(e1.grade) as grade_count from (select e.ename, s.grade from emp e, salgrade s where e.sal between s.losal and s.hisal) e1 group by e1.grade order by e1.grade;
```

***key：count函数***

#### 反思

能不能添加一个grade字段

### 13、面试题：

有 3 个表 S(学生表)，C（课程表），SC（学生选课表）
S（SNO，SNAME）代表（学号，姓名）
C（CNO，CNAME，CTEACHER）代表（课号，课名，教师）
SC（SNO，CNO，SCGRADE）代表（学号，课号，成绩）
问题：
1，找出没选过“黎明”老师的所有学生姓名。
2，列出 2 门以上（含2 门）不及格学生姓名及平均成绩。
3，即学过 1 号课程又学过 2 号课所有学生的姓名。

#### 思路

1，找出没选过“黎明”老师的所有学生姓名

```sql
select s.sname from (select sc.sno from (select c.cno from c where c.cteacher = '黎明') c1 where sc.cno = c1.cno) sc1 where sc1.sno = s.sno;
```

***key：多表联查与子语句***

2，列出 2 门以上（含2 门）不及格学生姓名及平均成绩

```sql
select a.sno, count(a.scgrade) as count from (select sc.sno, sc.scgrade from sc where sc.scgrade < 60) a group by a.sno having count(a.scgrade) >= 2; //显示出2门及以上不及格学生的学号
```

```sql
select s.sno, s.sname from s inner join (select a.sno, count(a.scgrade) as count from (select sc.sno, sc.scgrade from sc where sc.scgrade < 60) a group by a.sno having count(a.scgrade) >= 2) b on s.sno = b.sno;  //显示出2门及以上不及格学生的姓名及学号
```

```sql
select sc.sno, avg(scgrade) as avg from sc group by sc.sno; //显示出所有学生的平均成绩及学号
```

```sql
select d.sno, d.avg from (select sc.sno, avg(scgrade) as avg from sc group by sc.sno) d inner join (select a.sno, count(a.scgrade) as count from (select sc.sno, sc.scgrade from sc where sc.scgrade < 60) a group by a.sno having count(a.scgrade) >= 2) c on c.sno = d.sno; //显示出2门及以上不及格学生的平均成绩及学号
```

```sql
select e1.sname, e2.avg from (select s.sno, s.sname from s inner join (select a.sno, count(a.scgrade) as count from (select sc.sno, sc.scgrade from sc where sc.scgrade < 60) a group by a.sno having count(a.scgrade) >= 2) b on s.sno = b.sno) e1, (select d.sno, d.avg from (select sc.sno, avg(scgrade) as avg from sc group by sc.sno) d inner join (select a.sno, count(a.scgrade) as count from (select sc.sno, sc.scgrade from sc where sc.scgrade < 60) a group by a.sno having count(a.scgrade) >= 2) c on c.sno = d.sno) e2 where e1.sno = e2.sno; //列出2门及以上不及格学生姓名及平均成绩
```

***key：from中的子查询结果可以看成一张临时表***

3，既学过 1 号课程又学过 2 号课所有学生的姓名

```sql
select sc.sno from sc where sc.cno = '1'; //学过1号课程的学生学号
```

```sql
select sc.sno from sc where sc.cno = '2'; //学过2号课程的学生学号
```

```sql
select sc1.sno from (select sc.sno from sc where sc.cno = '1') sc1, (select sc.sno from sc where sc.cno = '2') sc2 where sc1.sno = sc2.sno; //既学过1号课程又学过2号课所有学生的学号
```

```sql
select s.sname from s, (select sc1.sno from (select sc.sno from sc where sc.cno = '1') sc1, (select sc.sno from sc where sc.cno = '2') sc2 where sc1.sno = sc2.sno) sc3 where s.sno = sc3.sno; //既学过1号课程又学过2号课所有学生的姓名

select s.sname from s inner join (select sc1.sno from (select sc.sno from sc where sc.cno = '1') sc1, (select sc.sno from sc where sc.cno = '2') sc2 where sc1.sno = sc2.sno) sc3 on s.sno = sc3.sno; //上面的sql语句使用from多表查询，此sql语句是用join多表查询，上网查询了一下好像两者没有什么区别，效率是一样的
```

#### 反思

having语句中使用分组函数是不能使用需要分组的字段的别名的，必须是该字段使用了分组函数后形成的名字

from多表查询与join多表查询有什么区别

### 14、列出所有员工及领导的姓名

#### 思路

```sql
select e1.ename as staff, e2.ename as manger from emp e1, emp e2 where e1.mgr = e2.empno;
```

#### 反思

无

### 15、列出受雇日期早于其直接上级的所有员工的编号,姓名,部门名称

#### 思路

1.找出没有重复的领导人表

```sql
select distinct e.mgr from emp e;
```

2.找出没有重复的领导人入职日期表

```sql
select e.empno, e.hiredate from emp e inner join (select distinct e.mgr from emp e) e1 on e.empno = e1.mgr;
```

3.找出早于直接上级的领导人入职的员工表（妥妥的社畜表:cry:)

```sql
select e3.empno, e3.ename, e3.deptno from emp e3 inner join (select e.empno, e.hiredate from emp e inner join (select distinct e.mgr from emp e) e1 on e.empno = e1.mgr) e2 on e2.empno = e3.mgr and e2.hiredate > e3.hiredate;
```

***key：消重***

#### 反思

在解析的过程中，我一直困扰能不能跳过第一步，直接第二步--就是用一个sql语句找出没有重复的领导人入职日期表，但是消重只能一个字段，或者多个字段联合起来，对于一个字段消重同时另一个字段不做处理的情况无能为力

### 16、 列出部门名称和这些部门的员工信息, 同时列出那些没有员工的部门

#### 思路

```sql
select d.dname, e.empno, e.ename, e.job, e.mgr, e.hiredate, e.sal, e.comm, e.deptno from dept d left join emp e on d.deptno = e.deptno;
```

***key：外连接***

#### 反思

无

### 17、列出至少有 5 个员工的所有部门

#### 思路

1.找出至少有五个员工的部门

```sql
select count(e.deptno) as num from emp e group by e.deptno having count(e.deptno) >= 5;
```

2.显示出部门

```sql
select d.dname from dept d, (select e.deptno, count(e.deptno) as num from emp e group by e.deptno having count(e.deptno) >= 5) e where e.deptno = d.deptno;
```

***key：分组***

#### 反思

### 18、列出薪金比"SMITH" 多的所有员工信息

#### 思路

```sql
select e.empno, e.ename, e.job, e.mgr, e.hiredate, e.sal, e.comm, e.deptno from emp e inner join (select e.sal from emp e where e.ename = 'smith') e1 on e.sal > e1.sal;
```

#### 反思

无

### 19、 列出所有"CLERK"( 办事员) 的姓名及其部门名称, 部门的人数

#### 思路

1.列出所有"CLERK"( 办事员) 的姓名

```sql
select e.ename from emp e where e.job = 'clerk';
```

2.列出所有"CLERK"( 办事员) 的姓名及其部门名称

```sql
select e1.ename, d.dname from dept d inner join (select e.ename, e.deptno from emp e where e.job = 'clerk') e1 on e1.deptno = d.deptno;
```

3.部门的人数

```sql
select e.deptno, count(e.deptno) as num from emp e group by e.deptno;
```

4.列出所有"CLERK"( 办事员) 的姓名及其部门名称, 部门的人数

```sql
select e1.ename, e1.dname, e2.num from (select e1.ename, d.dname, d.deptno from dept d inner join (select e.ename, e.deptno from emp e where e.job = 'clerk') e1 on e1.deptno = d.deptno) e1, (select e.deptno, count(e.deptno) as num from emp e group by e.deptno) e2 where e1.deptno = e2.deptno;
```

#### 反思

无

### 20、列出最低薪金大于 1500 的各种工作及从事此工作的全部雇员人数

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