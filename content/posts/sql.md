+++
title = 'SQL语句笔记'
date = 2022-03-30T20:27:21+08:00
draft = false
categories = ["学习"]
+++

## 本文所使用的数据库

```sql
//学生：学号S#, 姓名Sname, 性别Ssex, 年龄Sage, 所属系别D#，班级Sclass
Student (S# char(8),Sname char(10),Ssex char(2),Sage integer,D# char(2),Sclass char(6))

//院系：系别D#，系名Dname, 系主任Dean
Dept (D# char(2),Dname char(10),Dean char(10))

//课程：课号C#, 课名Cname, 教师编号T#,  学时Chours，学分Credit
Course (C# char(3),Cname char(12),Chours integer,Credit float(1),T# char(3))

//教师：教师编号T#，教师名Tname, 所属院系D#，工资Salary
Teacher (T# char(3),Tname char(10),D# char(2),Salary float(2))

//选课：学号S#, 课号C#, 成绩Score
SC (S# char(8),C# char(3),Score float(1))
```

## 建立数据库

### 定义数据库和表

#### 创建Database

- **数据库(Database**)是若干具有相互关联关系的`Table/Relation`的集合
- 数据库可以看作是一个集中存放若干`Table`的大型文件

创建数据库的简单语法形式：

```sql
create database 数据库名;
```

示例：创建课程学习数据库SCT 

```sql
create database SCT;
```

#### 创建Table

创建table简单语法形式：

```sql
Create table 表名(列名 数据类型[Primary key|Unique][Notnull][, 列名 数据类型[Primary key|Unique][Notnull] , ... ]) ;
```

- **“[ ] ”表示其括起的内容可以省略，“| ”表示其隔开的两项可取其一**

- Primary key:主键约束。每个表只能创建一个主键约束。
- Unique:唯一性约束(即候选键)。可以有多个唯一性约束。
- Not null: 非空约束。是指该列允许不允许有空值出现，如选择了Not null表明该列不允许有空值出现。
- 语法中的数据类型在SQL标准中有定义

示例：定义学生表`Students`

```sql
Create Table Student(S# char(8) not null,Sname char(10),Ssex char(2),Sage integer,D# char(2),Sclass char(6));
```

示例：定义课程表`Course`

```sql
Create Table Course(C# char(3),Cname char(12),Chours integer,Credit float(1),T# char(3));
```

### 向表中追加元组

`insert into`简单语法形式：

```sql
insert into 表名[(列名[,列名]...] values(值[,值],...);
```

- `values`后面值的排列，须与`into`子句后面的列名排列一致
- 若表名后的所有列名省略，则`values`后的值的排列，须与该表存储中的列名排列一致

示例：追加学生表中的元组

```sql
//省略列名
Insert Into Student Values(98030101,'张三','男',20,03,980301);
//不省略列名
Insert Into Student( S#, Sname, Ssex, Sage, D# , Sclass) 
Values(98030102,'张四','女',20,03,980301);
```

## 利用SQL语言进行简单查询

### 单表查询

`Selec`t的简单语法形式：

```sql
Select 列名[[,列名]...] From 表名 [Where 检索条件];
```

- 语义：从表名所给出的表中，查询出满足检索条件的元组，并按给定的列名及顺序进行投影显示。
- 相当于：$\Pi$<sub>列名,...,列名</sub>($\sigma$<sub>检索条件</sub>(表名))
- Select语句中的`select ... , from... , where...`, 等被称为子句，在以上基本形式基础上会增加许多构成要素，也会增加许多新的子句，满足不同的需求。

示例：检索学生表中所有学生的信息

```sql
Select S#,Sname,Ssex,Sage,Sclass,D# From Student; 
Select * From Student; //如投影所有列，则可以用*来简写
```

示例：检索学生表中所有年龄小于等于19岁的学生的年龄及姓名

```sql
Select Sage,Sname From Student Where Sage <= 19;//投影的列可以重新排定顺序
```

### 检索条件的书写

与选择运算$\sigma$<sub>con</sub>( R )的条件`con`书写一样，只是其逻辑运算符用`and,or,not`来表示, 同时也要注意运算符的优先次序及括弧的使用。书写要点是注意对自然语言检索条件的正确理解。

示例：检索教师表中所有工资少于1500元或者工资大于2000元 并且是03系的教师姓名

```sql
Select Tname 
From Teacher
Where (Salary < 1500 or Salary > 2000) and D# = 03;
```

### 结果唯一性问题

关系模型不允许出现重复元组。但现实**DBMS**，却允许出现重复元组，但也允许无重复元组。

在**Table**中要求无重复元组是通过定义`Primary key`或`Unique`来保证的；而在检索结果中要求无重复元组, 是通过`DISTINCT`保留字的使用来实现的。

```sql
Select S# 
From SC Where Score > 80;//有重复元组出现，比如一个同学两门以上课程大于80
Select DISTINCT S# 
From SC Where Score > 80;//重复元组被DISTINCT过滤掉，只保留一份
```

### 结果排序问题

**DBMS**可以对检索结果进行排序，可以升序排列，也可以降序排列。

`Select`语句中结果排序是通过增加`order by`子句实现的。

```sql
order by 列名 [asc|desc]
```

意义为检索结果按指定列名进行排序，若后跟`asc`或省略，则为升序；若后跟`desc`, 则为降序。

示例：按学号由小到大的顺序显示出所有学生的学号及姓名

```sql
Select S#, Sname 
From Student Order By S# ASC;
```

示例：检索002号课大于80分的所有同学学号并按成绩由高到低顺序显示

```sql
Select S#  
From SC Where C# = 002 and Score > 80
Order By Score DESC;
```

### 模糊查询问题

比如检索姓张的学生，检索张某某；这类查询问题，`Select`语句是通过在检索条件中引入运算符`like`来表示的。

含有like运算符的表达式:

```sql
列名 [not] like '字符串'
```

- 找出匹配给定字符串的字符串。其中给定字符串中可以出现`%, _`等匹配符

| 符号 | 功能                                                                                                          |
| ---- | ------------------------------------------------------------------------------------------------------------- |
| `%`  | 匹配零个或多个字符                                                                                            |
| `_`  | 匹配任意单个字符                                                                                              |
| `\`  | 转义字符，用于去掉一些特殊字符的特定含义，使其被作为普通字符看待, 如用`\%`去匹配字符`%`，用`\_` 去匹配字符`_` |

示例：检索所有姓张的学生学号及姓名

```sql
Select S#, Sname
From Student Where Sname Like '张%';
```

### 多表联合查询

`Select`的多表联合检索语句

```sql
Select 列名[ [, 列名] ... ] 
From 表名1, 表名2, ...
Where 检索条件;
```

- 相当于$\Pi$<sub>列名,...,列名</sub>($\sigma$<sub>检索条件</sub>($表名1\times 表名2\times ...$))
- 检索条件中要包含连接条件，通过不同的连接条件可以实现等值连接、不等值连接及各种$\theta$-连接
- 多表连接时，如两个表的属性名相同，则需采用`表名.属性名`方式来限定该属性是属于哪一个表

示例：按‘数据库’课成绩由高到低顺序显示所有同学姓名(三表连接)

```sql
Select Sname From Student, SC, Course 
Where Student.S# = SC.S# and SC.C# = Course.C# and Cname = '数据库'
Order ByScore DESC;
```

### 重名处理

连接运算涉及到重名的问题，如两个表中的属性重名，连接的两个表重名(同一表的连接)等，因此需要使用别名以便区分

`select`中采用别名的方式

```sql
Select 列名 as 列别名[ [, 列名as  列别名] ... ]
From 表名1 as 表别名1, 表名2 as 表别名2,...
Where 检索条件;
```

- 上述定义中的`as`可以省略
- 当定义了别名后，在检索条件中可以使用别名来限定属性

示例：求有薪水差额的任意两位教师

```sql
Select T1.Tname as Teacher1, T2.Tname as Teacher2
From Teacher T1, Teacher T2
Where T1.Salary > T2.Salary ;
```

示例：求既学过“001”号课又学过 “002”号课的所有学生的学号

```sql
Select S1.S# From SC S1,SC S2
Where S1.S# = S2.S# and S1.C# = 001 and S2.C# = 002;
//注意这里必须要将一个表与自己做乘积，不能直接使用S1.C#='001' and S2.C# = '002'判断
```

## 利用SQL语言进行增-删-改

### 添加元组

元组新增`Insert`命令有两种形式

单一元组新增命令形式：插入一条指定元组值的元组

```sql
insert into 表名[(列名[，列名]...)] values (值[，值]...)；
```

批数据新增命令形式：插入子查询结果中的若干条元组，待插入的元组由子查询给出

```sql
insert into 表名 [(列名[，列名]...)] 子查询;
```

示例：单一元组新增

```sql
Insert Into Teacher (T#, Tname, D#, Salary) 
Values  (005, '阮小七', 03, 1250);
```

示例：批元组新增

```sql
Insert Into St (S#, Sname)
Select  S#, Sname From Student
Where Sname like '%伟';
```

### 删除元组

元组删除`Delete`命令: 删除满足指定条件的元组

```sql
Delete From 表名 [Where 条件表达式];
```

如果`Where`条件省略，则删除所有的元组。

示例：删除98030101号同学所选的所有课程

```sql
Delete From SC Where S# = 98030101;
```

示例：删除自动控制系的所有同学

```sql
Delete From Student Where D# 
in(Select D# 
From Dept Where Dname = '自动控制');
```

### 更新元组

元组更新`Update`命令: 用指定要求的值更新 指定表中满足指定条件的元组的 指定列的值

```sql
Update 表名 
Set 列名 = 表达式|(子查询)[ [,列名=表达式|(子查询)]... ] 
[Where条件表达式];
```

如果`Where`条件省略，则更新所有的元组。

示例：将所有计算机系的教师工资上调10%

```sql
Update Teacher Set Salary = Salary * 1.1 
Where D# 
in (Select D# From Dept Where Dname = '计算机');
```

## 修正与撤销数据库

### 修正数据库

修正基本表的定义

```sql
alter table tablename
[add {colname datatype, ...}]//增加新列
[drop {完整性约束名}]//删除完整性约束
[modify {colname datatype, ...}]//修改列定义
```

示例：在学生表`Student(S#,Sname,Ssex,Sage,D#,Sclass)`基础上增加二列`Saddr, PID`

```sql
Alter Table Student Add Saddr char[40],PID char[18];
```

示例：将上例表中`Sname`列的数据类型修改为10个字符

```sql
Alter Table Student Modify Sname char(10);
```

示例：删除学生姓名必须取唯一值的约束

```sql
Alter Table Student Drop Unique(Sname);
```

### 撤销与修改

撤消基本表

```sql
drop table 表名
```

示例：撤消学生表`Student`

```sql
Drop Table Student;
```

注意，`delete`语句只是删除表中的元组,而撤消基本表`drop table`的操作是撤消包含表格式、表中所有元组、由该表导出的视图等相关的所有内容，所以使用要特别注意。

撤消数据库

```sql
drop database 数据库名; 
```

示例：撤消`SCT`数据库

```sql
Drop database SCT;
```

## 利用SQL语言表达复杂查询

### IN与NOT IN谓词子查询

基本语法：

```sql
表达式 [not] in (子查询)
```

- 语法中，表达式的最简单形式就是列名或常数。

- 语义：判断某一表达式的值是否在子查询的结果中。

示例：列出张三、王三同学的所有信息

```sql
Select * From Student
Where Sname in ('张三', '王三');
//可以使用枚举的方式查询
```

示例：列出选修了001号课程的学生的学号和姓名

```sql
Select S#  
From SC Where C# = 001 and S#   
in( Select S# From SC Where C# = 002);
```

示例：列出没学过李明老师讲授课程的所有同学的姓名

```sql
Select Sname From Student Where S# 
not in( Select S# From SC,Course C,Teacher T Where T.Tname = '李明' and SC.C# = C.C# and T.T# = C.T#);
```

### Some与All子查询

基本语法：

```sql
表达式 x some (子查询)
表达式 x all (子查询)
```

- 语法中，x是比较运算符：< , > , >= , <= , = , <>。
- 如果表达式的值至少与子查询结果的某一个值相比较满足$\theta$关系，则`some`结果便为真；
- 如果表达式的值与子查询结果的所有值相比较都满足$\theta$关系，则`all`结果便为真；

示例：找出工资最低的教师姓名

```sql
Select Tname From Teacher
Where Salary <= all(Select Salary From Teacher);         
```

示例：找出001号课成绩不是最高的所有学生的学号

```sql
Select S# From SC
Where C# = 001 and Score <  
some ( Select Score From SC Where C# = 001);
```

如下两种表达方式含义是相同的

```sql
表达式 = some (子查询)
表达式 in (子查询)
//
表达式 not in (子查询)
表达式 <> all (子查询)
```

### (NOT) EXISTS 子查询

基本语法：

```sql
[not] Exists (子查询)
```

语义：子查询结果中有无元组存在

示例：检索选修了赵三老师主讲课程的所有同学的姓名

```sql
Select DISTINCT Sname From Student
Where exists (Select * From SC, Course, Teacher Where SC.C# = Course.C# and SC.S# = Student.S# and Course.T# = Teacher.T# and Tname = '赵三') 
```

> `exists`和`in`的区别是前者对元组作选择，后者对值做选择

不加`not`形式的`Exists`谓词可以不用，比如上面例子就可以直接写成：

```sql
Select DISTINCT Sname From Student, SC, Course, Teacher
Where SC.C# = Course.C# and SC.S# = Student.S# and Course.T# = Teacher.T# and Tname = '赵三') 
```

然而`not exists`却可以实现很多新功能

示例：检索学过001号教师主讲的所有课程的所有同学的姓名

```sql
Select Sname From Student
Where not exists//不存在
(Select * From Course//有一门001教师主讲课程
Where Course.T# = 001 and not exists//该同学没学过 
(Select * From SC
Where S# = Student.S# and C# = Course.C#));
```

## 结果计算

**Select-From-Where**语句中，`Select`子句后面不仅可是列名，而且可是一些计算表达式或聚集函数，表明在投影的同时直接进行一些运算

```sql
Select 列名|expr|agfunc(列名)[[,列名|expr|agfunc(列名)]...]
From 表名1 [,表名2 ...]
[Where 检索条件];
```

- `expr`可以是常量、列名、或由常量、列名、特殊函数及算术运算符构成的算术运算式。特殊函数的使用需结合各自DBMS的说明书
- `agfunc()`是一些聚集函数

示例：求有差额(差额>0)的任意两位教师的薪水差额

```sql
Select T1.Tname as TR1, T2.Tname as TR2,T1.Salary – T2.Salary
From Teacher T1, Teacher T2
Where T1.Salary > T2.Salary;
```

## 聚集函数

**SQL**提供了五个作用在简单列值集合上的内置聚集函数`agfunc`,分别是：`COUNT、SUM、AVG、MAX、MIN`

**SQL**聚集函数的参数类型、结果类型与作用如下：

![聚集函数表](https://cdn.jsdelivr.net/gh/aahowe/image@main/sqlfunc.png)

示例：求计算机系教师的工资总额

```sql
Select Sum(Salary) From Teacher T,Dept
Where Dept.Dname = '计算机' and Dept.D# = T.D#;
```

## 分组查询

**SQL**可以将检索到的元组按照某一条件进行分类，具有相同条件值的元组划到一个组或一个集合中，同时处理多个组或集合的聚集运算。

```sql
Select 列名|expr|agfunc(列名)[[,列名|expr|agfunc(列名)]...]
From 表名1 [,表名2 ...]
[Where 检索条件];
[Group by 分组条件];
```

分组条件可以是:`列名1, 列名2, ...`

示例： 求每一个学生的平均成绩

```sql
Select S#,AVG(Score) From SC
Group by S#;
```

> 上例是按学号进行分组，即学号相同的元组划到一个组中并求平均值![对同一个人的成绩求均值](https://cdn.jsdelivr.net/gh/aahowe/image@main/QQ20220331-164001@2x.png)

## 分组过滤

若要对集合(即分组)进行条件过滤，即满足条件的集合/分组留下，不满足条件的集合/分组剔除。

`Having`子句，又称分组过滤子句。需要有`Group by子`句支持，换句话说，没有`Group by`子句，便不能有`Having`子句。

```sql
Select 列名|expr|agfunc(列名)[[,列名|expr|agfunc(列名)]...]
From 表名1 [,表名2 ...]
[Where 检索条件];
[Group by 分组条件[Having 分组过滤条件]];
```

示例：求不及格课程超过两门的同学的学号

```sql
Select S# From SC
Where Score < 60
Group by S# Having Count(*) > 2;     
```

## 利用SQL语言实现关系代数操作

**SQL**语言：并运算`UNION`, 交运算`INTERSECT`, 差运算`EXCEPT`。

基本语法形式：

```sql
子查询 {Union [ALL]|Intersect [ALL]|Except [ALL] 子查询}
```

通常情况下自动删除重复元组：不带`ALL`。若要保留重复的元组，则要带`ALL`。

假设子查询1的一个元组出现m次，子查询2的一个元组出现n次，则该元组在：

```sql
子查询1 Union ALL 子查询2//出现m+n次
子查询1 Intersect ALL 子查询2//出现min(m,n)次
子查询1 Except ALL 子查询2//出现max(0,m – n)次
```

### SQL并运算

示例：求学过002号课的同学或学过003号课的同学学号

```sql
Select S# From SC Where C# = 002
UNION
Select S# From SC Where C# = 003;
```

> 上述语句也可采用如下不用UNION的方式来进行
>
> ```sql
> Select S# From SC Where C# = 002 OR C# = 003;
> ```

但有时也不能完全转换成不用`UNION`的方式

已知两个表*Customers(CID, Cname, City, Discnt)   Agents(AID, Aname, City, Percent)*

求客户所在的或者代理商所在的城市

```sql
Select City From Customers
UNION
Select City From Agents;
```

### SQL交运算

示例：求既学过002号课，又学过003号课的同学学号

```sql
Select S# From SC Where C# = 002
INTERSECT
Select S# From SC Where C# = 003;
```

> 上述语句也可采用如下不用INTERSECT的方式来进行
>
> ```sql
> Select S# From SC Where C# = 002 and S# IN 
> (Select S# From SC Where C# = 003);
> ```

交运算符`Intersect`并没有增强SQL的表达能力，没有`Intersect`，**SQL**也可以用其他方式表达同样的查询需求。只是有了`Intersect`更容易表达一些，但增加了**SQL**语言的不唯一性。

### SQL差运算

示例： 假定所有学生都有选课，求没学过002号课程的学生学号

```sql
Select DISTINCT S# From SC
EXCEPT
Select S# From SC Where C# = 002;
```

> 前述语句也可不用EXCEPT的方式来进行
>
> ```sql
> Select DISTINCT S# From SC SC1 
> Where not exists (Select * From SC 
> Where C# = 002 and S# = SC1.S#);
> ```

差运算符`Except`也没有增强**SQL**的表达能力，没有`Except`， **SQL**也可以用其他方式表达同样的查询需求。只是有了`Except`更容易表达一些，但增加了**SQL**语言的不唯一性。

## 空值的处理

在**SQL**标准中和许多现流行的**DBMS**中，空值被用一种特殊的符号`Null`来标记，使用特殊的空值检测函数来获得某列的值是否为空值。

空值检测

```sql
is [not] null
```

示例：找出年龄值为空的学生姓名

```sql
Select Sname From Student 
Where Sage is null;
```

注意：上例条件不能写为`Where Sage = null`空值是不能进行运算的

现行DBMS的空值处理小结

- 除`is [not] null`之外，空值不满足任何查找条件
- 如果`null`参与算术运算，则该算术表达式的值为`null`
- 如果`null`参与比较运算，则结果可视为`false`。在**SQL-92**中可看成`unknown`
- 如果`null`参与聚集运算，则除`count(*)`之外其它聚集函数都忽略`null`利用**SQL**语言实现关系代数操作

## 内连接、外连接

关系代数运算中，有连接运算，又分为$\theta$连接和外连接

标准**SQL**语言中连接运算通常是采用

```sql
Select 列名|expr|agfunc(列名)[[,列名|expr|agfunc(列名)]...]
From 表名1 [,表名2 ...]//在这里进行外连接
[Where 检索条件];
```

即相当于采用 $\Pi$<sub>列名,...,列名</sub>($\sigma$<sub>检索条件</sub>($表名1\times 表名2\times ...$))

**SQL**的高级语法中引入了内连接与外连接运算，具体形式：

```sql
Select ...
From 表名1 [NATURAL]
[INNER|{ LEFT | RIGHT | FULL} [OUTER]] JOIN 表名2
{ ON 连接条件| Using (Colname {, Colname...})}
[Where 检索条件]...;
```

上例的连接运算由两部分构成：连接类型和连接条件

![连接类型&连接条件](https://cdn.jsdelivr.net/gh/aahowe/image@main/sqllink.png)

`Inner Join`:  即关系代数中的$\theta$-连接运算

`Left Outer Join, Right  Outer Join, Full  Outer  Join`: 即关系代数中的外连接运算

- 如`表1 Left Outer Join表2`，则连接后，表1的任何元组t都会出现在结果表中，如表2中有满足连接条件的元组s, 则t与s连接；否则t与空值元组连接；
- 如`表1 Right Outer Join表2`，则连接后，表2的任何元组s都会出现在结果表中，如表1中有满足连接条件的元组t, 则t与s连接；否则s与空值元组连接；
- 如`表1 Full Outer Join表2`，是前两者的并。

连接中使用`natural`:**出现在结果关系中的两个连接关系的元组在公共属性上取值相等，且公共属性只出现一次**

连接中使用`on <连接条件>`:**出现在结果关系中的两个连接关系的元组取值满足连接条件，且公共属性出现两次**

连接中使用`using (Col1, Col2, ..., Coln)`:**只判断using括号内的条件相等即可以连接，且这些条件只出现一次**

示例: 求所有教师的任课情况并按教师号排序(没有任课的教师也需列在表中)

```sql
Select Teacher.T#, Tname, Cname
From Teacher Inner Join Course
ON Teacher.T# = Course.T#
Order by Teacher.T# ASC;
```

示例: 求所有教师的任课情况(没有任课的教师也需列在表中)

```sql
Select Teacher.T#, Tname, Cname
From Teacher Left Outer JoinCourse
ON Teacher.T# = Course.T#
Order by Teacher.T# ASC;
```

## 视图的定义

### 定义视图

```sql
create view view_name [(列名[，列名] ...)]   
as 子查询 [with check option]
```

- 如果视图的属性名缺省，则默认为子查询结果中的属性名；也可以显式指明其所拥有的列名。
- `with check option`指明当对视图进行`insert，update，delete`时，要检查进行操作的元组是否满足视图定义中子查询中定义的条件表达式

示例：定义一个视图 `CompStud `为计算机系的学生，通过该视图可以将`Student`表中其他系的学生屏蔽掉

```sql
Create View CompStud AS
(Select * From Student Where D# in(Select D# From Dept 
 Where Dname = '计算机'));
```

示例：定义视图`StudStat`, 描述学生的平均成绩、最高成绩，最低成绩等

```sql
Create View StudStat(S#, Sname, AvgS, MinS, MaxS, CNT)
as (Select S#, Sname, AVG(Score), MIN(Score), Max(Score), Count(*)     
From Student S, SC Where S.S# = SC.S#
Group by S.S#);
```

### 使用视图

定义好的视图，可以像`Table`一样，在**SQL**各种语句中使用

示例：检索计算机系的年龄小于20的所有学生，我们可使用前文定义的`CompStud`视图

```sql
Select * From CompStud Where Sage < 20;
```

示例：基于前文视图`StudStat`检索某一学生平均成绩

```sql
Select Sname,AvgSFrom StudStat Where Sname = '张三';
```

### SQL视图更新

SQL视图更新的可执行性:

- 如果视图的`select`目标列包含聚集函数，则不能更新
- 如果视图的`select`子句使用了`unique`或`distinct`，则不能更新
- 如果视图中包括了`group by`子句，则不能更新
- 如果视图中包括经算术表达式计算出来的列，则不能更新
- 如果视图是由单个表的列构成，但并没有包括主键，则不能更新

**对于由单一`Table`子集构成的视图，即如果视图是从单个基本表使用选择、投影操作导出的，并且包含了基本表的主键，则可以更新**

可更新SQL视图示例：

```sql
create view CStud(S#, Sname, Sclass)
as (select S#, Sname, Sclass
from Student where D# = 03);
```

上例是可以更新的

```sql
Insert into CStud
Values (98030104, '张三丰', 980301);
```

### 撤消视图

撤销视图语句

```sql
Drop View view_name
```

示例：撤消视图Teach

```sql
Drop View CompStud;
```

## 数据库的完整性

### 静态完整性

#### Col_constr列约束

```sql
{ NOT NULL |//列值非空
[ CONSTRAINT constraintname]//为约束命名，便于以后撤消
{ UNIQUE//列值是唯一
| PRIMARY KEY//列为主键
| CHECK  (search_cond)//列值满足条件,条件只能使用列当前值
| REFERENCES tablename[(colname)]
[ON DELETE { CASCADE | SET NULL }]}} 
//引用另一表tablename的列colname的值，如有ON DELETE CASCADE 或ON DELETE SET NULL语句，则删除被引用表的某列值v时，要将本表该列值为v的记录删除或列值更新为null；缺省为无操作。
```

>Col_constr列约束：只能应用在单一列上，其后面的约束如`UNIQUE, PRIMARY KEY`及`search_cond`只能是单一列唯一、单一列为主键、和单一列相关

示例

```sql
Create Table Student (S# char(8) not null unique,Sname char(10),Ssex char(2)  constraint ctssex check (Ssex='男' or Ssex='女'),Sage integer check (Sage>=1  and Sage<150),D# char(2) references Dept(D#) on delete cascade,Sclass char(6));
//假定Ssex只能取{男，女}, 1=<Sage<=150, D#是外键
```

示例

```sql
Create Table Course (C# char(3),Cname char(12),Chours integer,Credit float(1) constraint ctcredit check (Credit >= 0.0 and Credit <= 5.0),T# char(3)  references Teacher(T#) on delete cascade);
//假定每门课学分最多5分，最少0分
```

#### table_constr表约束

```sql
[ CONSTRAINT constraintname]//为约束命名，便于以后撤消
{ UNIQUE (colname{, colname. . .})//几列值组合在一起是唯一
| PRIMARY KEY  (colname {, colname. . .})//几列联合为主键
| CHECK (search_condition)//元组多列值共同满足条件,条件中只能使用同一元组的不同列
| FOREIGN KEY (colname {, colname. . .})
REFERENCES tablename [(colname {, colname. . .})]
[ON DELETE CASCADE] }//引用另一表tablename的若干列的值作为外键
```

> table_constr表约束：是应用在关系上，即对关系的多列或元组进行约束，列约束是其特例

示例

```sql
Create Table Student (S# char(8) not null unique,Sname char(10),Ssex char(2)  constraint ctssex check (Ssex='男' or Ssex='女'),Sage integer check (Sage>1  and Sage<150),D# char(2) references Dept(D#) on delete cascade,Sclass char(6), primary key(S#));
//S#为主键，这种情况下与单列约束没什么区别
```

示例

```sql
Create Table Course (C# char(3),Cname char(12),Chours integer,Credit float(1) constraint ctcredit check (Credit >= 0.0 and Credit <= 5.0),T# char(3)   references Teacher(T#) on delete cascade,primary key(C#),constraint ctcc  check(Chours/Credit = 20));  
//假定严格约束20学时一个学分
```

示例

```sql
Create Table SC (S# char(8),C# char(3),Score float(1) constraint ctscore check  (Score >= 0.0 and Score <= 100.0),forergn key (S#) references student(S#) on delete cascade,forergn key (C#) references course(C#) on delete cascade);
//S#/C#都为外键
```

**`check`可以为子查询**

#### 更改约束

`Create Table`中定义的表约束或列约束可以在以后根据需要进行撤消或追加。撤消或追加约束的语句是`Alter Table`(不同系统可能有差异)

```sql
ALTER TABLE tblname
...
...
[MODIFY ( columnnamedata-type [DEFAULT {default_const|NULL}] [[NOT]NULL]{,columnname...})]
[ADD CONSTRAINT constr_name]
[DROP CONSTRAINT constr_name]
[DROP PRIMARY KEY] 
```

示例：撤消SC表的ctscore约束(由此可见，未命名的约束是不能撤消)

```sql
Alter Table SCDROP CONSTRAINT ctscore;
```

#### 断言ASSERTION

**SQL**还提供了复杂条件表达的断言。其语法形式为：

```sql
CREATE ASSERTION <assertion-name> CHECK <predicate>
```

- 一个断言就是一个谓词表达式，它表达了希望数据库总能满足的条件
- 表约束和列约束就是一些特殊的断言
- 当一个断言创建后，系统将检测其有效性，并在每一次更新中测试更新是否违反该断言。
- 断言测试增加了数据库维护的负担，要小心使用复杂的断言。

示例:“每笔贷款,要求至少一位借款者账户中存有最低数目的余额，例如1000元”

> *borrower(customer_name, loan_number,...)*//客户及其贷款(一笔贷款的借款者)
>
> *account(account_number,..., balance)*//账户及其余额
>
> *depositor(account_number, customer_name)*//客户及其账户(一个借款者的账户)
>
> *loan(loan_number, amount)*//每一笔贷款

```sql
create assertion balance_constraint check
(not exists(select * fromloan  
where not exists( select * fromborrower, depositor,account
where loan.loan_number = borrower.loan_number
and borrower.customer_name = depositor.customer_name
and depositor.account_number = account.account_number
and account.balance >= 1000)))
```

### 动态完整性

`Create Table`中的表约束和列约束基本上都是静态的约束，也基本上都是对单一列或单一元组的约束(尽管有参照完整性)，为实现动态约束以及多个元组之间的完整性约束，就需要触发器技术`Trigger`

`Trigger`是一种过程完整性约束(相比之下，`Create Table`中定义的都是非过程性约束),是一段程序，该程序可以在特定的时刻被自动触发执行，比如在一次更新操作之前执行，或在更新操作之后执行。

```sql
CREATE TRIGGER trigger_name BEFORE | AFTER
{INSERT | DELETE | UPDATE [OFcolname {, colname...}] }
ON tablename [ REFERENCING corr_name_def {, corr_name_def...}]
[FOR EACH ROW | FOR EACH STATEMENT]
//对更新操作的每一条结果(前者)，或整个更新操作完成(后者)
[WHEN(search_condition)]//检查条件，如满足执行下述程序
{statement//单行程序直接书写，多行程序要用下行方式
|BEGIN ATOMICstatement; { statement;...} END}
```

触发器`Trigger`意义：当某一事件发生时(`Before|After`),对该事件产生的结果(或是每一元组，或是整个操作的所有元组), 检查条件`search_condition`, 如果满足条件，则执行后面的程序段。条件或程序段中引用的变量可用`corr_name_def`来限定。

事件：`BEFORE | AFTER { INSERT | DELETE | UPDATE...}`

- 当一个事件(`Insert, Delete, Update`)发生之前`Before`或发生之后`After`触发
- 操作发生，执行触发器操作需处理两组值：更新前的值和更新后的值，这两个值由`corr_name_def`的使用来区分

![发生操作的瞬间](https://cdn.jsdelivr.net/gh/aahowe/image@main/QQ20220404-113627@2x.png)

`corr_name_def`的定义:

```SQL
{ OLD [ROW] [AS]old_row_corr_name//更新前的旧元组命别名为
| NEW [ROW] [AS]new_row_corr_name//更新后的新元组命别名为
| OLD TABLE [AS]old_table_corr_name//更新前的旧Table命别名为
| NEW TABLE [AS]new_table_corr_name//更新后的新Table命别名为}
```

`corr_name_def`将在检测条件或后面的动作程序段中被引用处理

示例：设计一个触发器当进行Teacher表更新元组时, 使其工资只能升不能降

```sql
create trigger teacher_chgsal before update of salary 
on teacher
referencing new x,old y 
for each row when(x.salary < y.salary)
begin 
raise_application_error(-20003, 'invalid salary on update'); 
//此条语句为Oracle的错误处理函数
end;
```

---

## 结尾

本文是在学习SQL语句的时候为了能方便查阅做的笔记，基本上和慕课上的ppt是一模一样的。

> 数据库系统（上）：模型与语言
>
> https://www.icourse163.org/course/HIT-1001516002
