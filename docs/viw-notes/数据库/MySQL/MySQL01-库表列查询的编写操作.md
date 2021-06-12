[TOC]



# 库表列查询的操作





## 库操作

### 1.创建库

```sql
CREATE DATABASE database-name
```

### 2.删除数据库

```sql
drop database db_name
```



## 表操作







## 列操作



## 查询





>3.创建新表
>`create table table_name(col1 type1 [not null] [primary key], col2 type2 [not null]  )`

>4.1增加一个列
>`alter table table_name add colum col_new type_new`

>4.2删除列
>
>(删除表中的一个列(column) yy)
>`alter table table_name drop column yy`

>4.3修改列字段类型和长度 modify 
>修改表table_name的 Sname的数据类型， varchar->text
>`alter table table_name modify Sname text`
>
>```sql
>-- student 表，需要将 name 字段由 char(30) 修改为 varchar(66)
>alter table student modify column stu_name varchar(60);
>```



> 修改某一个字段的数据值
>
> ```sql
> // update 表名 set 列名 = 新值,列名 = 新值... where 列名 = 某值
> update student set name = 'a' where id = 1;
> ```
>
> 



>4.4修改列名(同时可以修改字段类型)
>将表table_name 的gender 改变成sex
>`alter table table_name change gender sex text`

>4.5修改表名：
>
>将表名table_name 修改改为 stu
>
>`alter table  table_name rename to stu `



>5.添加主键
>`alter table table_name add primary key(colum_name)`

>6.创建索引
>`create [unique] index index_name on table_name (colunm)`

>7.删除索引
>`drop index index_name`

>8.创建视图
>`create view view_name as select statement`

>9.数据库分页
>`select * from table limit (start-1)*limit,limit`
>start是页码,limit是每页显示的条数

>10.使用distinct关键字去除重复行
>`select distinct colum_name from table_name`

>11.删除表中数据
- drop table table_name
>删除内容和定义，释放空间。是把整个表都删除，并且之后不能新增数据，只能新建表
- truncate table table_name
 >删除内容，只是清空数据(保留表的数据结构)。truncate不是删除行数据， 
- delete from table_name where colum_name='colum_name'
>删除表中的行，每次执行只是删除表中的一行，会把这个删除操作写进日志中，可能以后用于事务rollback



> 12.把表的数据插入另外一个表
>
> ```sql
> create table table_name as select * from table_old_name
> ```



四张表("Student","Score","Teacher","Course")

~~~java

CREATE TABLE `Student`(
`s_id` VARCHAR(20),
`s_name` VARCHAR(20) NOT NULL DEFAULT '',
`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);

~~~

~~~java
CREATE TABLE `Score`(
`s_id` VARCHAR(20),
`c_id` VARCHAR(20),
`s_score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);

~~~

~~~java
CREATE TABLE `Teacher`(
`t_id` VARCHAR(20),
`t_name` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);

~~~

~~~java
CREATE TABLE `Course`(
`c_id` VARCHAR(20),
`c_name` VARCHAR(20) NOT NULL DEFAULT '',
`t_id` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);

~~~



![](C:/Program Files/Typora/typora0828/usr/uploads/2020/04/2359370492.png)

---

-- 分组




SELECT s_sex,COUNT(s_id)

FROM student

GROUP BY s_sex

---


SELECT c_id,COUNT(s_id)
from score
GROUP BY c_id

- 分组结果的条件
- 

SELECT s.s_id,avg(s.s_score)

from score as s

GROUP BY s.s_id

HAVING avg(s.s_score)>60


---

- 查询至少选修两门课程的学生学号

select 查询结果 [学号,每个学生选修课程数目：汇总函数count]

from 从哪张表中查找数据 [课程的学生学号：课程表score]

where 查询条件 [至少选修两门课程：需要先计算出每个学生选修了多少门课，需要用分组，所以这里没有where子句]

group by 分组 [每个学生选修课程数目：按学号分组，然后用汇总函数count计算出选修了多少门课]

having 对分组结果指定条件

[至少选修两门课程：每个学生选修课程数目>=2]

SELECT s.s_id,COUNT(s.c_id)

from score as s

GROUP BY s.s_id

HAVING COUNT(s.c_id)>2

---

- 查询同名同性学生名单并统计同名人数

select 查询结果 [姓名,人数：汇总函数count(*)]

from 从哪张表中查找数据 [学生表student]

where 查询条件 [没有]

group by 分组 [姓名相同：按姓名分组]

having 对分组结果指定条件 [姓名相同：count(*)>=2]

order by 对查询结果排序[没有];

SELECT s.s_name,COUNT(*)

from student as s 

GROUP BY s.s_name

having COUNT(*)>=2

---


- 查询不及格的课程并按课程号从大到小排列
- 
select 查询结果 [课程号]

from 从哪张表中查找数据 [成绩表score]

where 查询条件 [不及格：成绩 <60]

group by 分组 [没有]

having 对分组结果指定条件 [没有]

order by 对查询结果排序[课程号从大到小排列：降序desc];

SELECT s.c_id

from score as s

where s.s_score<60

order by s.c_id desc

---


- 查询每门课程的平均成绩，结果按平均成绩升序排序，平均成绩相同时，按课程号降序排列

select 查询结果 [课程号,平均成绩：汇总函数avg(成绩)]

from 从哪张表中查找数据 [成绩表score]

where 查询条件 [没有]

group by 分组 [每门课程：按课程号分组]

having 对分组结果指定条件 [没有]

order by 对查询结果排序[按平均成绩升序排序:asc，平均成绩相同时，按课程号降序排列:desc];

select s.c_id,avg(s.s_score)

from score as s
	
group by s.c_id 

order by avg(s.s_score) asc, s.c_id desc; 


---

- 检索课程编号为“0004”且分数小于60的学生学号，结果按按分数降序排列

分析思路

select 查询结果 []

from 从哪张表中查找数据 [成绩表score]

where 查询条件 [课程编号为“04”且分数小于60]

group by 分组 [没有]

having 对分组结果指定条件 []

order by 对查询结果排序[查询结果按按分数降序排列];

select s.s_id

from score as s

where s.c_id = '0004' and s.s_score <60

ORDER BY s.s_score desc

---


- 统计每门课程的学生选修人数(超过2人的课程才统计)
要求输出课程号和选修人数，查询结果按人数降序排序，若人数相同，按课程号升序排序

select 查询结果 [要求输出课程号和选修人数]

from 从哪张表中查找数据 []

where 查询条件 []

group by 分组 [每门课程：按课程号分组]

having 对分组结果指定条件[学生选修人数(超过2人的课程才统计)：每门课程学生人数>2]

order by 对查询结果排序[查询结果按人数降序排序，若人数相同，按课程号升序排序];

SELECT s.c_id,COUNT( s.s_id)

from score as s

GROUP BY s.c_id

HAVING	COUNT(s.s_id)>2

order by COUNT(s.s_id) desc ,s.c_id asc

---

- 查询两门以上不及格课程的同学的学号及其平均成绩

1）[两门以上][不及格课程]限制条件

2）[同学的学号及其平均成绩]，也就是每个学生的平均成绩，显示学号，平均成绩

分析过程：

第1步：得到每个学生的平均成绩，显示学号，平均成绩

第2步：再加上限制条件：

1）不及格课程

2）两门以上[不及格课程]：课程数目>2

第1步：得到每个学生的平均成绩，显示学号，平均成绩

select 查询结果 [学号,平均成绩：汇总函数avg(成绩)]

from 从哪张表中查找数据 [涉及到成绩：成绩表score]

where 查询条件 [没有]

group by 分组 [每个学生的平均：按学号分组]

having 对分组结果指定条件 [没有]

order by 对查询结果排序[没有];

select 查询结果 [学号,平均成绩：汇总函数avg(成绩)]

from 从哪张表中查找数据 [涉及到成绩：成绩表score]

where 查询条件 [限制条件：不及格课程，平均成绩<60]

group by 分组 [每个学生的平均：按学号分组]

having 对分组结果指定条件

[限制条件：课程数目>2,汇总函数count(课程号)>2]

order by 对查询结果排序[没有];

SELECT s.s_id,s.s_score

from score as s

where s.s_score<60

group by s.s_id

having count(s.c_id)>=2

---

- 查询所有课程成绩小于60分学生的学号、姓名

1.翻译成大白话

1）查询结果：学生学号，姓名

2）查询条件：所有课程成绩 < 60
的学生，需要从成绩表里查找，用到子查询


第1步，写子查询（所有课程成绩 < 60 的学生）

select 查询结果[学号]

from 从哪张表中查找数据[成绩表：score]

where 查询条件[成绩 < 60]

group by 分组[没有]

having 对分组结果指定条件[没有]

order by 对查询结果排序[没有]

limit 从查询结果中取出指定行[没有];

select  sc.s_id

from score as sc 

where sc.s_score < 60

group by 

having 

ORDER BY

第2步，查询结果：学生学号，姓名，条件是前面1步查到的学号

select 查询结果[学号,姓名]

from 从哪张表中查找数据[学生表:student]

where 查询条件[用到运算符in]

group by 分组[没有]

having 对分组结果指定条件[没有]

order by 对查询结果排序[没有]

limit 从查询结果中取出指定行[没有];


select student.s_id, student.s_name
from student  
where  student.s_id in (select  sc.s_id
from score as sc 
where sc.s_score < 60)
GROUP BY 
HAVING 
ORDER BY 

---

- 查询没有学全所有课的学生的学号、姓名

查找出学号，条件：没有学全所有课，也就是该学生选修的课程数 < 总的课程数

SELECT student.s_id,student.s_name

from student

where  student.s_id in  ( 	SELECT score.s_id 
					from score 
					GROUP BY score.s_id
					HAVING COUNT(score.c_id) < (SELECT COUNT(course.c_id) from course)
)

---

- 查询出只选修了两门课程的全部学生的学号和姓名

SELECT student.s_id,student.s_name

FROM	student 

WHERE   student.s_id  in( select score.s_id FROM score

                        GROUP BY score.s_id 
    
                        HAVING COUNT(score.c_id)=2)

group by 
HAVING
order by
LIMIT

---

- 查找1990年出生的学生名单
学生表中出生日期列的类型是datetime

select student.s_id,student.s_name

from student

where YEAR(student.s_birth)=1990

---
### 时间

SELECT CURRENT_DATE()

SELECT CURRENT_TIME()

SELECT CURRENT_TIMESTAMP()

SELECT DAYNAME(CURRENT_DATE())


---

###  联合查询 

- 查询所有学生的学号、姓名、选课数、总成绩

SELECT student.s_id,student.s_name,COUNT(score.c_id),sum(score.s_score)

from student LEFT JOIN  score on student.s_id = score.s_id

GROUP BY student.s_id

---


- 查询平均成绩大于85的所有学生的学号、姓名和平均成绩

SELECT  student.s_id,student.s_name,avg(score.s_score)

from student LEFT JOIN score on student.s_id = score.s_id

group by student.s_id

HAVING avg(score.s_score>85)

order by
LIMIT

---

- 查询学生的选课情况：学号，姓名，课程号，课程名称

SELECT student.s_id,student.s_name,course.c_id,course.c_name

from student INNER JOIN score ON student.s_id = score.s_id

INNER JOIN course on score.c_id = course.c_id

---

- 查询出每门课程的及格人数和不及格人数
考察case表达式


SELECT score.c_id, sum( CASE when score.s_score >=60 then 1 ELSE 0 END),
									sum( case when score.s_score < 60 then 1 else 0 END)
from score 
GROUP BY score.c_id

---

- 查询课程编号为'0003'且课程成绩在80分以上的学生的学号和姓名

SELECT student.s_id,student.s_name

from student

where student.s_id in(

SELECT score.s_id

from score 

where score.c_id = '03' AND score.s_score > 80

)