[01 | 数据和表：保存所有东西的地方](#01)   
[02 | SELECT语句：取得精美包装里的数据](#02)   
[03 | DELETE和UPDATE：改变是件好事](#03)   
[04 | 聪明的表设计：为什么要规范化？](#04)   
[05 | ALTER：改写历史](#05)   
[06 | SELECT进阶：以新视角看你的数据](#06)   
[07 | 多张表的数据库设计；拓展你的表](#07)   
[08 | 联接与多张表的操作：不能单独存在吗？](#08)   
[09 | 子查询：查询中的查询](#09)   
[10 | 外联接、自联接与联合：新策略](#10)   
[11 | 约束、视图与事务：人多手杂，数据库受不了](#11)   
[12 | 安全性：保护你的资产](#12)  


《SQL技术手册》--这本书列举了常用RDBMS的不同写法。

[**example code download**](https://resources.oreilly.com/examples/9780596526849/tree/master)

---
<h1 id="01">01 | 数据和表：保存所有东西的地方</h1>

---




* 在创建表前先把数据分类。尤其要注意每列的数据类型。
* 使用```CREATE DATABASE```语句来创建存储所有表的数据库。
* 使用```USE DATABASE```语句进入数据库，然后创建表。
* 所有表都以```CREATE TABLE```语句创建，句中包含列名及其数据类型。
* 一些常见的数据类型有 CHAR、VARCHAR、BLOB、INT、DEC、DATE、DATETIME、TIMESTAMP。每种数据类型的存储规则都不一样。
* 查看表结构可以使用命令```DESC my_table_name;```
* 删除表的命令```DROP TABLE my_table_name;```，其执行后会删除表结构和表中所有数据。

```
CREATE TABLE my_contacts
(
    last_name  VARCHAR(30)  null,
    first_name VARCHAR(20)  null,
    email      VARCHAR(50)  null,
    gender     CHAR(1)      null,
    birthday   DATE         null,
    profession VARCHAR(50)  null,
    location   VARCHAR(50)  null,
    status     VARCHAR(50)  null,
    interests  VARCHAR(100) null,
    seeking    VARCHAR(100) null
);
```

### 创建 INSERT 语句

**顺序很重要，数值的顺序必须和列名的顺序完全一样**

```
INSERT INTO my_contacts (last_name, first_name, email, gender, birthday, profession, location, status, interests, seeking) 
VALUES 
('Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto,CA', 'Single', 'Kayaking,Reptiles', 'Relationship,Friends');
```
任何属于VARCHAR、CHAR、DATE、DATETIME、TIMESTAMP、BLOB列类型的值都需要加单引号；DEC、INT等数值类型不需要加上单引号。


### 各种 INSERT 语句

 1.  **改变列顺序** 我们可以改变列名的顺序，只要记得数据值的顺序也要一起调整
 2.  **省略列名** 列名列表可以省略，但数值必须**全部填入**，而且必须与当初创建表时的**列顺序完全相同**
```
INSERT INTO my_contacts 
VALUES 
('Anderson', 'Jillian', 'jill_anderson@breakneckpizza.com', 'F', '1980-09-05', 'Technical Writer', 'Palo Alto,CA', 'Single', 'Kayaking,Reptiles', 'Relationship,Friends');
```
 3.  **省略部分列**  也可以只填入一部分列值就好了。

### NULL

NULL是未定义的值，它不等于零，也不等于空值。值可以是NULL，但绝非等于NULL。事实上，两个NULL根本不能放在一起比较。

没有在INSERT语句中被赋值的列默认为NULL。

可以把列修改为不接受NULL值，这需要在创建表时使用关键字NOT NULL。



### 用DEFAULT填补空白
如果某些列通常有某个特定值，我们就可以把特定值指派为 DEFAULT 默认值。跟在DEFAULT关键字后的值会在每次新增记录时自动插入表中--**只要没有另外指派其他值。**默认值的类型必须
和列的类型相同。








---
<h1 id="02">02 | SELECT语句：取得精美包装里的数据</h1>

---

### 文本中的单引号转义

```
mysql> INSERT INTO my_contacts (first_name,location,status) VALUES ('Funyon', 'Grover's MillNJ','Single');
    -> ;
    -> ;
    -> ';
1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 's MillNJ','Single');

;

;

'' at line 1
mysql> 
```

每次按下“Enter”都看到提示符“->”

出错了，SQL程序期待收到成对的单引号，但在 Grover's Mill却把SQL搞糊涂了，它让单引号总数变成单数。SQL RDBMS还在等待能让程序结束的最后一个单引号。
输入单引号和分号即可结束语句。此时也会看到相应的错误提示。

单引号时特殊字符，当单引号作为文本的一部分，并非表示文本的结束，我们需要在单引号字符的前面加上**反斜线**就能到达效果。
```
INSERT INTO my_contacts (first_name,location,status) VALUES ('Funyon', 'Grover\'s MillNJ','Single');
```


**是否能使用双引号取代单引号？**   
不要使用双引号，因为你的SQL语句日后会搭配其他编程语言（如PHP）。在编程语言中使用“”表示“从这里开始是SQL语句”，这样单引号才会被视为
SQL语句的一部分，而不是其他编程语言的一部分。


**另一种帮引号“转义’**的方式则是在它前面再加一个单引号。
```
INSERT INTO my_contacts (first_name,location,status) VALUES ('Funyon', 'Grover''s MillNJ','Single');
```

### SELECT 特定列以加快结果呈现

只选择需要的列是一个值得遵循的编程惯例，不过它还有其他的好处。随着表的日益扩大，限定选择列还会加快检索结果的速度。


### 对文本数据套用比较运算符

比较像 CHAR 和 VARCHAR 这样的文本数据时，运作方式个数字其实很相似。

比较运算符会按字母顺序地评估所有事物的大小。

### 别把 AND 和 OR 搞混了

需要所有条件都成立时，请用 AND，需要任何条件成立时，请用 OR。

### like关键字通常结合两个通配符使用
* %，百分号表示任意数量的未知字符的替身
* _，下划线表示一个未知字符的替身



### BETWEEN ... AND

范围的起止也会包含再查找范围内。BETWEEN 等于使用 <= 加 >= 。

对于字符范围查询，MySQL 包含 BETWEEN 项，不包含 AND项（不同的而数据库有不同的操作方式）。

比如我们需要使用“O”之后的字母，以确保我们能获取到以字母“O”开头的饮料名称。
```
SELECT drink_name
FROM drink_info
WHERE drink_name BETWEEN 'G' AND 'P'
ORDER BY drink_name;
```

BETWEEN ... AND  用再数值类型字段时，较小的数值必须先交给 BETWEEN，解释的结果才会如同我们所期待的。

### NOT

NOT 可以和 BETWEEN 或 LIKE 一起使用，重点是**NOT 一定要紧接在 WHERE 后面**

```
SELECT drink_name
FROM drink_info
WHERE not calories < 30 OR calories > 60
ORDER BY drink_name;

SELECT drink_name
FROM drink_info
WHERE not calories BETWEEN 30 AND 60
ORDER BY drink_name;
```

当 NOT 和 AND 或 OR一起使用时，则要直接接在 AND 或 OR 的后面。
```
SELECT date_name
FROM black_book
WHERE NOT date_name LIKE 'A%'
  AND NOT date_name LIKE 'B%';

SELECT *
FROM easy_drinks
WHERE NOT main = 'soda'
  AND NOT main = 'iced tea';
```

Q：刚才说 NOT必须紧接在WHERE之后，如果是使用NOT IN呢？   
A：NOT IN 是个例外。而且即使把NOT移到WHERE后也可以运作，下面两组语句会返回相同的结果。   
```
SELECT *
FROM easy_drinks
WHERE NOT main IN ('soda', 'iced tea');

SELECT *
FROM easy_drinks
WHERE main NOT IN ('soda', 'iced tea');
```

Q：对<>（不等）运算符而言，套用 NOT 的方式也一样吗？   
A：是可以这么做，但不就成了双重否定了吗？此时用等号更合理。以下两组语句会返回相同结果。   
```
SELECT *
FROM easy_drinks
WHERE NOT drink_name <> 'Blackhron';

SELECT *
FROM easy_drinks
WHERE drink_name = 'Blackhron';
```

Q：NOT 可以套用在NULL上吗？   
A：可以。要取得某列中所有不是NULL的值，可以这样查询。   
```
SELECT *
FROM easy_drinks
WHERE NOT main IS NULL;

# 这样查询也可以

SELECT *
FROM easy_drinks
WHERE main IS NOT NULL;
```









---
<h1 id="03">03 | DELETE和UPDATE：改变是件好事</h1>

---


除非你可以非常确定 WHERE 子句只会删除/更新你打算删除/更新的行，否则都应该用 SELECT 确认情况。

UPDATE 语句能用在表的多条记录上。它可以和基础数学运算符一起使用，可以操作数值数据。文本变量也可以，例如UPPER() 
函数、LOWER() 函数等。


---
<h1 id="04">04 | 聪明的表设计：为什么要规范化？</h1>

---

**使用数据的方式将影响设置表的方式**。


### 创建表时，可遵循如下步骤

1. 挑出事物，跳出你希望表描述的**某样事物**。
2. 列出一份关于那样事物的**信息列表**，这些信息都是**使用表**时的表要信息。
3. 使用信息列表，把关于那样事物的综合信息拆分成小块信息，以便用于组织表。


### 原子性数据

当数据具有**原子性（atomic）**，就表示它已经被分割至**最小块，已经不能或不应该再被分割**。

### 原子性数据和你的表

1. 你的表在描述什么事物？
2. 以何种方式使用表取得描述的事物呢？
3. 列是否包含原子性数据，可让查询既简短又直逼要害？

Q：原子不是很小吗？我是不是应该把数据分割成非常非常小的片段吗？   
A：不是。让数据具有原子性，表示把数据分割成创建有效率的表所需要的最小片段。别把数据切割得超出必要。
如果不需要额外增加列，就别因为可以增加而增加。

Q：原子性对我又什么帮助？   
A：原子性有助于确保表内容的准确性。例如，你有一个门牌号码列，你可以确保有关名牌号码的数字只会出现在
该列中。

原子性数据也能使查询更有效率，因为查询会因原子性而更容易设计，而且运行所需时间也更短，因此在面对大量
数据时有加分效果。