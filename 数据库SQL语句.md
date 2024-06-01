## SQL语句(仅供个人期末复习使用)

### 数据定义语言（DDL）

一个关系数据库管理系统的实例（Instance）中可以建立多个数据库，一个数据库中可以建立多个模式，一个模式下通常包括多个表、视图和索引等数据库对象。因此SQL的数据定义功能包括定义模式、定义表、定义视图和定义索引。视图是基于基本表的虚表，索引是依附于基本表的，因此SQL通常不提供修改视图定义和修改索引定义的操作。用户如果想修改视图定义或索引定义，只能先将它们删除掉，然后再重建。不过有些关系数据库产品如Oracle允许直接修改视图定义。
![img](https://img-blog.csdnimg.cn/img_convert/29d33a5290f85c8b9f2201eaf7b7ee79.png)

#### 模式

##### 模式的定义

定义模式实际上定义了一个命名空间，在这个空间中可以定义该模式包含的数据库对象，例如基本表、视图、索引等。

在CREATE SCHEMA中可以接受CREATE TABLE，CREATE VIEW和GRANT子句。

```SQL
CREATE SCHEMA <模式名> AUTHORIZATION <用户名> [<表定义子句>|<视图定义子句>|<授权定义子句>];
```

案例演示：为用户ZHANG创建了一个模式TEST，并且在其中定义一个表TAB1。

```sql
CREATE SCHEMA TEST AUTHORIZATION ZHANG CREATE TABLE TAB1 (
  COL1 SMALLINT,
  COL2 INT,
  COL3 CHAR(20),
  COL4 NUMERIC (10, 3),
  COL5 DECIMAL (5, 2)
) ;
```

##### 模式的删除

```sql
DROP SCHEMA <模式名> <CASCADE|RESTRICT>
```

```
CASCADE（级联）：删除模式的同时把该模式中所有的数据库对象全部删除。
RESTRICT（限制）：如果该模式中定义了下属的数据库对象（如表、视图等），则拒绝该删除语句的执行，仅当该模式中没有任何下属的对象时才能执行。
```

案例演示：删除模式ZHANG，同时该模式中定义的表TAB1也被删除。

```sql
DROP SCHEMA ZHANG CASCADE;
```

#### 基本表

##### 基本表的定义

建立数据库最重要的一步就是定义一些基本表，SQL语言使用CREATE TABLE语句定义基本表。

```sql
CREATE TABLE <表名> (
    <列名> <数据类型>[ <列级完整性约束条件> ]
  [,<列名> <数据类型>[ <列级完整性约束条件> ]]
  [,<列名> <数据类型>[ <列级完整性约束条件> ]]    
    ...
  [,<表级完整性约束条件> ]
);
```

```sql
<表名>：所要定义的基本表的名字。
<列名>：组成该表的各个属性（列）。
<列级完整性约束条件>：涉及相应属性列的完整性约束条件。
<表级完整性约束条件>：涉及一个或多个属性列的完整性约束条件 。
如果完整性约束条件涉及到该表的多个属性列，则必须定义在表级上，否则既可以定义在列级也可以定义在表级。
```

案例演示：建立“学生”表Student，学号是主码，姓名取值唯一。

```sql
CREATE TABLE Student (
  Sno CHAR(9) PRIMARY KEY,
  Sname CHAR(20) UNIQUE,
  Ssex CHAR(2),
  Sage SMALLINT,
  Sdept CHAR(20)
) ;
```

案例演示：建立一个“课程”表Course，课程号是主码。

```sql
CREATE TABLE Course (
  Cno CHAR(4) PRIMARY KEY,
  Cname CHAR(40),
  Cpno CHAR(4),
  Ccredit INT
) ;
```

案例演示：建立一个学生选课表SC，学生号和课程号组成主码。

```sql
CREATE TABLE SC (
  Sno CHAR(9),
  Cno CHAR(4),
  Grade DECIMAL (40, 2) ,
  /* 主码由两个属性构成，必须作为表级完整性进行定义 */
  PRIMARY KEY (Sno, Cno),
  /* 表级完整性约束条件，Sno是外码，被参照表是Student */
  FOREIGN KEY (Sno) REFERENCES Student (Sno),
  /* 表级完整性约束条件，Cno是外码，被参照表是Course */
  FOREIGN KEY (Cno) REFERENCES Course (Cno)
) ;
```

**模式与表：**

每一个基本表都属于某一个模式，一个模式包含多个基本表，定义基本表所属模式有以下三种方式：

- 方法一：在表名中明显地给出模式名
  - Create table"S-T".Student(…);
  - Create table “S-T”.Cource(…);
  - Create table “S-T”.SC(…);
- 方法二：在创建模式语句中同时创建表
- 方法三：设置所属的模式

创建基本表（其他数据库对象也一样）时，若没有指定模式，系统根据搜索路径来确定该对象所属的模式，关系数据库管理系统会使用模式列表中第一个存在的模式作为数据库对象的模式名，若搜索路径中的模式名都不存在，系统将给出错误。

##### 基本表的修改

```sql
ALTER TABLE <表名>
[ ADD  [ COLUMN ] <新列名> <数据类型> [ 完整性约束 ] ]
[ ADD <表级完整性约束>]
[ DROP [ COLUMN ] <列名> [CASCADE| RESTRICT] ]
[ DROP CONSTRAINT <完整性约束名> [ RESTRICT | CASCADE ] ]
[ ALTER COLUMN <列名><数据类型> ] ;
```

```
<表名>是要修改的基本表。
ADD子句用于增加新列、新的列级完整性约束条件和新的表级完整性约束条件。
DROP COLUMN子句用于删除表中的列。
如果指定了CASCADE短语，则自动删除引用了该列的其他对象。
如果指定了RESTRICT短语，则如果该列被其他对象引用，关系数据库管理系统将拒绝删除该列。
DROP CONSTRAINT子句用于删除指定的完整性约束条件。
ALTER COLUMN子句用于修改原有的列定义，包括修改列名和数据类型。
```

案例演示：向Student表增加“入学时间”列，其数据类型为日期型。

```
注意：不管基本表中原来是否已有数据，新增加的列一律为空值.
```

```sql
ALTER TABLE Student 
ADD S_entrance DATE ;
```

案例演示：将年龄的数据类型由字符型（假设原来的数据类型是字符型）改为整数。

```sql
ALTER TABLE Student 
ALTER COLUMN Sage INT ;
```

案例演示：增加课程名称必须取唯一值的约束条件。

```sql
ALTER TABLE Course 
ADD UNIQUE (Cname) ;
```

##### 基本表的删除

```sql
DROP TABLE <表名> ［RESTRICT | CASCADE］ ;
```

```
CASCADE（级联）：在删除基本表的同时，相关的依赖对象一起删除。
RESTRICT（限制）：欲删除的基本表不能被其他表的约束所引用，如果存在依赖该表的对象，则此表不能被删除。
```

案例演示：删除Student表。

```sql
DROP TABLE Student CASCADE ;
```

DROP TABLE时，SQL2011 与 3个RDBMS的处理策略比较：

![img](https://img-blog.csdnimg.cn/img_convert/f28509653ca5366aa7fce70fb8709403.png)

#### 视图

##### 视图的定义

视图是一个虚拟表，其内容由查询定义。但视图并不存储数据，视图数据来自由定义视图的查询所引用的表。关系数据库管理系统执行CREATE VIEW语句时只是把视图定义存入数据字典，并不执行其中的SELECT语句。在对视图查询时，按视图的定义从基本表中将数据查出。视图能够简化用户的操作，使用户能以多种角度看待同一数据，视图对重构数据库提供了一定程度的逻辑独立性，能够对机密数据提供安全保护，适当的利用视图可以更清晰的表达查询。

```sql
CREATE VIEW < 视图名 > [ (< 列名 > [, < 列名 > ] …) ] AS
< 子查询 > [ WITH CHECK OPTION ] ;
```

```
WITH CHECK OPTION：对视图进行UPDATE，INSERT和DELETE操作时要保证更新、插入或删除的行满足视图定义中的谓词条件（即子查询中的条件表达式）。子查询可以是任意的SELECT语句，是否可以含有ORDER BY子句和DISTINCT短语，则决定具体系统的实现。
```

**基于单表的视图：**

案例演示：建立信息系学生的视图。

```sql
CREATE VIEW IS_Student AS 
SELECT 
  Sno,
  Sname,
  Sage 
FROM
  Student 
WHERE Sdept = 'IS' ;
```

案例演示：建立信息系学生的视图，并要求进行修改和插入操作时仍需保证该视图只有信息系的学生。

```sql
CREATE VIEW IS_Student AS 
SELECT 
  Sno,
  Sname,
  Sage 
FROM
  Student 
WHERE Sdept = 'IS' WITH CHECK OPTION ;
```

**基于多表的视图：**

案例演示：建立信息系选修了1号课程的学生的视图（包括学号、姓名、成绩）。

```sql
CREATE VIEW IS_S1 (Sno, Sname, Grade) AS 
SELECT 
  Student.Sno,
  Sname,
  Grade 
FROM
  Student,
  SC 
WHERE Sdept = 'IS' AND Student.Sno = SC.Sno AND SC.Cno = '1' ;
```

**基于视图的视图：**

案例演示：建立信息系选修了1号课程且成绩在90分以上的学生的视图。

```sql
CREATE VIEW IS_S2 AS 
SELECT 
  Sno,
  Sname,
  Grade 
FROM
  IS_S1 
WHERE Grade >= 90 ;
```

**带表达式的视图：**

案例演示：定义一个反映学生出生年份的视图。

```sql
CREATE VIEW BT_S (Sno, Sname, Sbirth) AS 
SELECT 
  Sno,
  Sname,
  2014-Sage 
FROM
  Student ;
```

**分组视图：**

案例演示：将学生的学号及平均成绩定义为一个视图。

```sql
CREATE VIEW S_G (Sno, Gavg) AS 
SELECT 
  Sno,
  AVG(Grade) 
FROM
  SC 
GROUP BY Sno ;
```

##### 数据的修改

案例演示：将信息系学生视图IS_Student中学号”201215122”的学生姓名改为”刘辰”。

```sql
UPDATE 
  IS_Student 
SET
  Sname = '刘辰' 
WHERE Sno = '201215122' ;
```

案例演示：向信息系学生视图IS_S中插入一个新的学生记录，其中学号为”201215140”，姓名为”赵新”，年龄为20岁。

```sql
INSERT INTO IS_Student 
VALUES('201215140','赵新',20) ;
```

案例演示：删除信息系学生视图IS_Student中学号为”201215140”的记录。

```sql
DELETE FROM IS_Student 
WHERE Sno = '201215140' ;
```

##### 视图的删除

```sql
DROP VIEW < 视图名 > [ CASCADE ] ;
```

```
如果该视图上还导出了其他视图，使用CASCADE级联删除语句，把该视图和由它导出的所有视图一起删除 。
```

案例演示：删除视图BT_S。

```sql
DROP VIEW BT_S ;
```

#### 索引

##### 索引的定义

建立索引是加快查询速度的有效手段。用户可以根据应用环境的需要，在基本表上建立一个或多个索引，以提供多种存取路径，加快查找速度。一般说来，建立与删除索引由数据库管理员DBA或表的属主（即建立表的人）负责完成。系统在存取数据时会自动选择合适的索引作为存取路径，用户不必也不能选择索引。

在SQL语言中，建立索引使用CREATE INDEX语句。索引可以建立在该表的一列或多列上，各列名之间用逗号分隔。每个列名后面还可以用次序指定索引值的排列次序，可选ASC（升序）或DESC（降序），缺省值为ASC升序。

```sql
CREATE [UNIQUE] [CLUSTER] INDEX <索引名> 
ON <表名>(<列名>[<次序>][,<列名>[<次序>] ]…);
```

```
<表名>：要建索引的基本表的名字。
索引：可以建立在该表的一列或多列上，各列名之间用逗号分隔。
<次序>：指定索引值的排列次序，升序：ASC，降序：DESC。缺省值：ASC。
UNIQUE：此索引的每一个索引值只对应唯一的数据记录。
CLUSTER：表示要建立的索引是聚簇索引。所谓聚簇索引是指索引项的顺序与表中记录的物理顺序一致的索引组织。用户可以在最常查询的列上建立聚簇索引以提高查询效率。显然在一个基本表上最多只能建立一个聚簇索引。建立聚簇索引后，更新索引列数据时，往往导致表中记录的物理顺序的变更，代价较大，因此对于经常更新的列不宜建立聚簇索引。
```

案例演示：为Student，Course，SC三个表建立索引。Student表按学号升序建唯一索引，Course表按课程号升序建唯一索引，SC表按学号升序和课程号降序建唯一索引。

```sql
CREATE UNIQUE INDEX Stusno 
ON Student (Sno) ;

CREATE UNIQUE INDEX Coucno 
ON Course (Cno) ;

CREATE UNIQUE INDEX SCno 
ON SC (Sno ASC, Cno DESC) ;
```

##### 名称的修改

```sql
ALTER INDEX <旧索引名> RENAME TO <新索引名> ;
```

案例演示：将SC表的SCno索引名改为SCSno。

```sql
ALTER INDEX SCno RENAME TO SCSno;
```

##### 索引的删除

删除索引时，系统会从数据字典中删去有关该索引的描述。

```sql
DROP INDEX <索引名> on <表名>;
```

案例演示：删除Student表的Sname索引。

```sql
DROP INDEX Sname ON Student;
```

### 数据查询语言（DQL）

#### 单表查询

```sql
SELECT 
  [ ALL | DISTINCT ] 
  < 目标列表达式 > [,< 目标列表达式 > ] … 
FROM
  < 表名或视图名 > [,< 表名或视图名 > ] … | (SELECT 语句) [ AS ] < 别名 > 
[ WHERE < 条件表达式 > ] 
[ GROUP BY < 列名1 > [ HAVING < 条件表达式 > ] ] 
[ ORDER BY < 列名2 > [ ASC | DESC ] ] ;
```

```
SELECT子句：指定要显示的属性列。
FROM子句：指定查询对象（基本表或视图）。
WHERE子句：指定查询条件。
GROUP BY子句：对查询结果按指定列的值分组，该属性列值相等的元组为一个组。通常会在每组中作用聚集函数。
HAVING短语：只有满足指定条件的组才予以输出。
ORDER BY子句：对查询结果表按指定列值的升序或降序排序。
```

![img](https://img-blog.csdnimg.cn/img_convert/ad8866dbacbee4011bbac28fd86b4804.png)

##### 简单查询

案例演示：查询全体学生的学号与姓名。

```sql
SELECT Sno,Sname FROM Student ;
```

案例演示：查询全体学生的姓名、学号、所在系。

```sql
SELECT Sname,Sno,Sdept FROM Student ;
```

案例演示：查询全体学生的详细记录。

```sql
SELECT Sno,Sname,Ssex,Sage,Sdept FROM Student ;
```

```sql
SELECT * FROM Student ;
```

案例演示：查全体学生的姓名及其出生年份。（假设当年为2014）

```sql
SELECT Sname,2014-Sage FROM Student ;
```

案例演示：查询全体学生的姓名、出生年份和所在的院系，要求用小写字母表示系名。（假设当年为2014）

```sql
SELECT Sname,'Year of Birth:',2014-Sage,LOWER(Sdept) FROM Student ;
```

##### 条件查询

案例演示：查询计算机科学系全体学生的名单。

```sql
SELECT Sname FROM Student WHERE Sdept = 'CS' ;
```

案例演示：查询所有年龄在20岁以下的学生姓名及其年龄。

```sql
SELECT Sname,Sage FROM Student WHERE Sage < 20 ;
```

案例演示：查询考试成绩有不及格的学生的学号。

```sql
SELECT DISTINCT Sno FROM SC WHERE Grade < 60 ;
```

案例演示：查询年龄在20~23岁（包括20岁和23岁）之间的学生的姓名、系别和年龄。

```sql
SELECT Sname,Sdept,Sage FROM Student WHERE Sage BETWEEN 20 AND 23 ;
```

案例演示：查询年龄不在20~23岁之间的学生姓名、系别和年龄。

```sql
SELECT Sname,Sdept,Sage FROM Student WHERE Sage NOT BETWEEN 20 AND 23 ;
```

##### IN子查询

案例演示：查询计算机科学系（CS）、数学系（MA）和信息系（IS）学生的姓名和性别。

```sql
SELECT Sname,Ssex FROM Student WHERE Sdept IN ('CS','MA','IS') ;
```

案例演示：查询既不是计算机科学系、数学系，也不是信息系的学生的姓名和性别。

```sql
SELECT Sname,Ssex FROM Student WHERE Sdept NOT IN ('CS','MA','IS') ;
```

##### 模糊查询

谓词： [NOT] LIKE ‘<匹配串>’ [ESCAPE ‘ <换码字符>’]

- % （百分号） ：代表任意长度（长度可以为0）的字符串。
- _ （下横线）：代表任意单个字符。

案例演示：查询学号为201215121的学生的详细情况。

```sql
SELECT * FROM Student WHERE Sno LIKE '201215121' ;
```

```sql
SELECT * FROM Student WHERE Sno = '201215121' ;
```

案例演示：查询所有姓刘学生的姓名、学号和性别。

```sql
SELECT Sname,Sno,Ssex FROM Student WHERE Sname LIKE '刘%' ;
```

案例演示：查询姓"欧阳"且全名为三个汉字的学生的姓名。

```sql
SELECT Sname FROM Student WHERE Sname LIKE '欧阳__' ;
```

案例演示：查询名字中第2个字为"阳"字的学生的姓名和学号。

```sql
SELECT Sname,Sno FROM Student WHERE Sname LIKE '__阳%' ;
```

案例演示：查询所有不姓刘的学生姓名、学号和性别。

```sql
SELECT Sname,Sno,Ssex FROM Student WHERE Sname NOT LIKE '刘%' ;
```

案例演示：查询DB_Design课程的课程号和学分。

```
注意：使用换码字符将通配符转义为普通字符。
```

```sql
SELECT Cno,Ccredit FROM Course WHERE Cname LIKE 'DB#_Design' ESCAPE '#' ;
```

案例演示：查询以"DB_"开头，且倒数第3个字符为 i的课程的详细情况。

```sql
SELECT * FROM Course WHERE Cname LIKE 'DB#_%i__' ESCAPE '#' ;
```

##### 空值查询

案例演示：某些学生选修课程后没有参加考试，所以有选课记录，但没 有考试成绩。查询缺少成绩的学生的学号和相应的课程号。

```sql
SELECT Sno,Cno FROM SC WHERE Grade IS NULL ;
```

案例演示：查所有有成绩的学生学号和课程号。

```sql
SELECT Sno,Cno FROM SC WHERE Grade IS NOT NULL ;
```

##### 与或查询

```
案例演示：查询计算机系年龄在20岁以下的学生姓名。
```

```sql
SELECT Sname FROM Student WHERE Sdept = 'CS' AND Sage < 20 ;
```

案例演示：查询计算机科学系（CS）、数学系（MA）和信息系（IS）学生的姓名和性别。

```sql
SELECT Sname,Ssex FROM Student WHERE Sdept IN ('CS','MA','IS') ;
```

```sql
SELECT Sname,Ssex FROM Student WHERE Sdept = 'CS' OR Sdept = 'MA' OR Sdept = 'IS' ;
```

##### 排序查询

案例演示：查询选修了3号课程的学生的学号及其成绩，查询结果按分数降序排列。

```sql
SELECT Sno,Grade FROM SC WHERE Cno = '3' ORDER BY Grade DESC ;
```

案例演示：查询全体学生情况，查询结果按所在系的系号升序排列，同一系中的学生按年龄降序排列。

```sql
SELECT * FROM Student ORDER BY Sdept,Sage DESC ;
```

##### 聚合查询

案例演示：查询学生总人数。

```sql
SELECT COUNT(*) FROM Student ;
```

案例演示：查询选修了课程的学生人数。

```sql
SELECT COUNT(DISTINCT Sno) FROM SC ;
```

案例演示：计算1号课程的学生平均成绩。

```sql
SELECT AVG(Grade) FROM SC WHERE Cno = '1' ;
```

案例演示：查询选修1号课程的学生最高分数。

```sql
SELECT MAX(Grade) FROM SC WHERE Cno = '1' ;
```

案例演示：查询学生201215012选修课程的总学分数。

```sql
SELECT SUM(Ccredit) FROM SC, Course WHERE Sno = '201215012' AND SC.Cno = Course.Cno ;
```

##### 分组查询

案例演示：求各个课程号及相应的选课人数。

```sql
SELECT Cno,COUNT(Sno) FROM SC GROUP BY Cno ;
```

##### 过滤查询

案例演示：查询选修了3门以上课程的学生学号。

```sql
SELECT Sno FROM SC GROUP BY Sno HAVING COUNT(*) > 3 ;
```

案例演示：查询平均成绩大于等于90分的学生学号和平均成绩。

```sql
SELECT Sno,AVG(Grade) FROM SC GROUP BY Sno HAVING AVG(Grade) >= 90 ;
```

#### 连接查询

##### 等值连接

案例演示：查询每个学生及其选修课程的情况。

```sql
SELECT Student.*,SC.* FROM Student,SC WHERE Student.Sno = SC.Sno ;
```

##### 非等值连接

案例演示：查询选修2号课程且成绩在90分以上的所有学生的学号和姓名。

```sql
SELECT Student.Sno,Sname FROM Student,SC WHERE Student.Sno = SC.Sno AND SC.Cno = '2' AND SC.Grade > 90 ;
```

##### 自身连接

案例演示：查询每一门课的间接先修课（即先修课的先修课）。

```sql
SELECT 
  FIRST.Cno,
  SECOND.Cpno 
FROM
  Course FIRST,
  Course SECOND 
WHERE FIRST.Cpno = SECOND.Cno ;
```

##### 外连接

案例演示：查询每个学生及其选修课程的情况。

```sql
SELECT 
  Student.Sno,
  Sname,
  Ssex,
  Sage,
  Sdept,
  Cno,
  Grade 
FROM
  Student 
LEFT JOIN SC ON (Student.Sno = SC.Sno) ;
```

##### 多表连接

案例演示：查询每个学生的学号、姓名、选修的课程名及成绩。

```sql
SELECT 
  Student.Sno,
  Sname,
  Cname,
  Grade 
FROM
  Student,
  SC,
  Course 
WHERE Student.Sno = SC.Sno AND SC.Cno = Course.Cno ;
```

#### 嵌套查询

一个SELECT-FROM-WHERE语句称为一个查询块，将一个查询块嵌套在另一个查询块的WHERE子句或HAVING短语的条件中的查询称为嵌套查询，上层的查询块称为外层查询或父查询，下层查询块称为内层查询或子查询，SQL语言允许多层嵌套查询，即一个子查询中还可以嵌套其他子查询，子查询不能使用ORDER BY子句。

不相关子查询：子查询的查询条件不依赖于父查询，由里向外 逐层处理。即每个子查询在上一级查询处理之前求解，子查询的结果用于建立其父查询的查找条件。
相关子查询：子查询的查询条件依赖于父查询，首先取外层查询中表的第一个元组，根据它与内层查询相关的属性值处理内层查询，若WHERE子句返回值为真，则取此元组放入结果表，然后再取外层表的下一个元组，重复这一过程，直至外层表全部检查完为止。

##### 带有比较运算符的子查询

案例演示：找出每个学生超过他选修课程平均成绩的课程号。

```sql
SELECT 
  Sno,
  Cno 
FROM
  SC X
WHERE Grade >= 
  (
  SELECT 
    AVG(Grade) 
  FROM
    SC Y
  WHERE y.Sno = x.Sno
  ) ;
```

##### 带有IN谓词的子查询

案例演示：查询与“刘晨”在同一个系学习的学生。

```sql
SELECT 
  Sno,
  Sname,
  Sdept 
FROM
  Student 
WHERE Sdept IN 
  (
  SELECT 
    Sdept 
  FROM
    Student 
  WHERE Sname = '刘晨'
  ) ;
```

##### 带有ANY或ALL谓词的子查询

使用ANY或ALL谓词时必须同时使用比较运算，语义为：

> ANY：大于子查询结果中的某个值
> ALL：大于子查询结果中的所有值
> < ANY：小于子查询结果中的某个值
> < ALL：小于子查询结果中的所有值
> = ANY：大于等于子查询结果中的某个值
> = ALL：大于等于子查询结果中的所有值
> <= ANY：小于等于子查询结果中的某个值
> <= ALL：小于等于子查询结果中的所有值
> = ANY：等于子查询结果中的某个值
> = ALL：等于子查询结果中的所有值
> != ANY：不等于子查询结果中的某个值
> != ALL：不等于子查询结果中的任何一个值

案例演示：查询非计算机科学系中比计算机科学系任意一个学生年龄小的学生姓名和年龄。

```sql
SELECT 
  Sname,
  Sage 
FROM
  Student 
WHERE Sage < ANY (SELECT Sage FROM Student WHERE Sdept = 'CS') 
AND Sdept <> 'CS' ;
```

案例演示：查询非计算机科学系中比计算机科学系所有学生年龄都小的学生姓名及年龄。

```sql
SELECT 
  Sname,
  Sage 
FROM
  Student 
WHERE Sage < ALL (SELECT Sage FROM Student WHERE Sdept = 'CS') 
AND Sdept <> 'CS' ;
```

##### 带有EXISTS谓词的子查询

带有EXISTS谓词的子查询不返回任何数据，只产生逻辑真值“true”或逻辑假值“false”。

若内层查询结果非空，则外层的WHERE子句返回真值。
若内层查询结果为空，则外层的WHERE子句返回假值。
由EXISTS引出的子查询，其目标列表达式通常都用 * ，因为带EXISTS的子查询只返回真值或假值，给出列名无实际意义。

NOT EXISTS谓词：

若内层查询结果非空，则外层的WHERE子句返回假值。
若内层查询结果为空，则外层的WHERE子句返回真值。

案例演示：查询所有选修了1号课程的学生姓名。

```sql
SELECT 
  Sname 
FROM
  Student 
WHERE EXISTS 
  (SELECT 
    * 
  FROM
    SC 
  WHERE Sno = Student.Sno AND Cno = '1'
  ) ;
```

案例演示：查询没有选修1号课程的学生姓名。

```sql
SELECT 
  Sname 
FROM
  Student 
WHERE NOT EXISTS 
  (SELECT 
    * 
  FROM
    SC 
  WHERE Sno = Student.Sno AND Cno = '1'
  ) ;
```

#### 集合查询

##### 并操作UNION

案例演示：查询计算机科学系的学生及年龄不大于19岁的学生。

```sql
SELECT * FROM Student WHERE Sdept = 'CS' 
UNION
SELECT * FROM Student WHERE Sage <= 19 ;
```

```
UNION：将多个查询结果合并起来时，系统自动去掉重复元组。
UNION ALL：将多个查询结果合并起来时，保留重复元组。
```

##### 交操作INTERSECT

案例演示：查询计算机科学系的学生与年龄不大于19岁的学生的交集。

```sql
SELECT  * FROM Student WHERE Sdept = 'CS'
INTERSECT 
SELECT * FROM Student WHERE Sage <= 19 ;
```

实际上就是查询计算机科学系中年龄不大于19岁的学生。

```sql
SELECT * FROM Student WHERE Sdept = 'CS' AND Sage <= 19 ;
```

##### 差操作EXCEPT

案例演示：查询计算机科学系的学生与年龄不大于19岁的学生的差集。

```sql
SELECT * FROM Student WHERE Sdept = 'CS' 
EXCEPT 
SELECT * FROM Student WHERE Sage <= 19 ;
```

实际上是查询计算机科学系中年龄大于19岁的学生。

```sql
SELECT * FROM Student WHERE Sdept = 'CS' AND Sage > 19 ;
```

#### 派生表查询

子查询不仅可以出现在WHERE子句中，还可以出现在FROM子句中，这时子查询生成的临时派生表（Derived Table）成为主查询的查询对象。如果子查询中没有聚集函数，派生表可以不指定属性列，子查询SELECT子句后面的列名为其缺省属性。

案例演示：找出每个学生超过他自己选修课程平均成绩的课程号。

```sql
SELECT Sno,Cno FROM SC,(SELECT Sno,AVG(Grade) FROM SC GROUP BY Sno) AS Avg_sc(avg_sno,avg_grade) 
WHERE SC.Sno = Avg_sc.avg_sno 
AND SC.Grade >= Avg_sc.avg_grade ;
```

案例演示：查询所有选修了1号课程的学生姓名。

```sql
SELECT 
  Sname 
FROM
  Student,(SELECT Sno FROM SC WHERE Cno = '1') AS SC1 
WHERE Student.Sno = SC1.Sno ;
```

### 数据操纵语言（DML）

#### 插入

##### 插入一条数据

语法格式：

```sql
INSERT INTO < 表名 > [ (< 属性列1 > [, < 属性列2 > …) ] 
VALUES (< 常量1 > [, < 常量2 > ] …) ;
```

案例演示：将一个新学生元组（学号：201215128;姓名：陈冬;性别：男;所在系：IS;年龄：18岁）插入到Student表中。

```sql
INSERT INTO Student (Sno, Sname, Ssex, Sdept, Sage) 
VALUES('201215128','陈冬','男','IS',18) ;
```

##### 插入多条数据

语法格式：

```sql
INSERT INTO < 表名 > [ (< 属性列1 > [, < 属性列2 > …) ] 
VALUES { (< 常量1 > [, < 常量2 > ] …) } ... ;
```

案例演示：插入两条学生元组，插入信息自选。

```sql
INSERT INTO Student (Sno, Sname, Ssex, Sdept, Sage) 
VALUES('201215129','小明','男','MA',29), ('201215130','小黄','女','CS',30) ;
```

##### 带子查询的插入

语法格式：

```sql
INSERT INTO < 表名 > [ (< 属性列1 > [, < 属性列2 > …) ] 
子查询 ;
```

案例演示：对每一个系，求学生的平均年龄，并把结果存入数据库。

第一步：建表

```sql
CREATE TABLE Dept_age (
  Sdept CHAR(15) ,
  Avg_age INT
) ;
```

第二步：插入：

```sql
INSERT INTO Dept_age (Sdept, Avg_age) 
SELECT 
  Sdept,AVG(Sage) 
FROM
  Student 
GROUP BY Sdept ;
```

#### 修改

##### 修改一条数据

语法格式：

```sql
UPDATE 
  < 表名 > 
SET
  < 列名 >= < 表达式 > [,
  < 列名 >= < 表达式 > ] … 
[ WHERE < 条件 > ] ;
```

案例演示：将学生201215121的年龄改为22岁。

```sql
UPDATE 
  Student 
SET
  Sage = 22 
WHERE Sno = '201215121' ;
```

##### 修改多条数据

语法格式：

```sql
UPDATE 
  < 表名 > 
SET
  < 列名 >= < 表达式 > [,
  < 列名 >= < 表达式 > ] … ;
```

案例演示：将所有学生的年龄增加1岁。

```sql
UPDATE 
  Student 
SET
  Sage = Sage + 1 ;
```

##### 带子查询的修改

语法格式：

```sql
UPDATE 
  < 表名 > 
SET
  < 列名 >= < 表达式 > [,
  < 列名 >= < 表达式 > ] … 
[ WHERE < 带子查询的条件 > ] ;
```

案例演示：将计算机科学系全体学生的成绩置零。

```sql
UPDATE 
  SC 
SET
  Grade = 0 
WHERE Sno IN (SELECT Sno FROM Student WHERE Sdept = 'CS') ;
```

#### 删除

##### 删除一条数据

语法格式：

```SQL
DELETE FROM < 表名 > 
[ WHERE < 条件 > ] ;
```

案例演示：删除学号为201215128的学生记录。

```SQL
DELETE FROM Student 
WHERE Sno = '201215128' ;
```

##### 删除多条数据

语法格式：

```sql
DELETE FROM < 表名 > ;
```

案例演示：删除所有的学生选课记录。

```sql
DELETE FROM SC ;
```

##### 带子查询的删除

语法格式：

```sql
DELETE FROM < 表名 > 
[ WHERE < 带子查询的条件 > ] ;
```

案例演示：删除计算机科学系所有学生的选课记录。

```sql
DELETE FROM SC 
WHERE Sno IN (SELECT Sno FROM Student WHERE Sdept = 'CS') ;
```

