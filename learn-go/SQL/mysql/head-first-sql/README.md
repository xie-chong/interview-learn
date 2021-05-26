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

[MySQL 函数](https://www.runoob.com/mysql/mysql-functions.html)

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


**原子性数据的正式规则：**

1. 具有原子性数据的列中不会有多个类型相同的的值。

| food_name  | ingredients  |
| ------------ | ------------ |
| bread  | flour,milk,egg,yeast,oil  |
| salad  | lettuce,tomato,cucumber  |

设想在列 ingredients中如何查找番茄？

2. 具有原子性数据的表中不会有多个存储同类数据的列。

| teacher  | student1  | student2  | student3  |
| ------------ | ------------ | ------------ | ------------ |
| Ms.Martini  | Joe  | Ron  | Kelly  |
| Mr.Howard  | Sanjaya  | Tim  | Julie  |

**规范化表的优点**

1. 规范化的表中没有重复的数据，可以减小数据库的大小
2. 因为查找的数据较少，你的查询会更为快速

### SHOW 命令

```
# 查看创建表的ddl语句
SHOW CREATE TABLE table_name;

# 列的细节信息
SHOW COLUMNS FROM tablename;

SHOW CREATE DATABASE databasename;

SHOW INDEX FROM tablename;

# 如果你从控制台收到SQL命令造成的错误信息，键入如下命令就可取得确切的警告内容。
SHOW WARNINGS;
```


```
SHOW CREATE TABLE my_contacts;
# 显示结果
CREATE TABLE `my_contacts` (
  `last_name` varchar(30) DEFAULT NULL,
  `first_name` varchar(20) DEFAULT NULL,
  `email` varchar(50) DEFAULT NULL,
  `gender` char(1) DEFAULT NULL,
  `birthday` date DEFAULT NULL,
  `profession` varchar(50) DEFAULT NULL,
  `location` varchar(50) DEFAULT NULL,
  `status` varchar(20) DEFAULT NULL,
  `interests` varchar(100) DEFAULT NULL,
  `seeking` varchar(100) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1
```

Q：SHOW CREATE TABLE中的反撇号究竟是做什么用的？我真的不需要用到反撇号吗？   
A：反撇号的存在是因为RDBMS有时无法辨出列名。如果在列名前后加上反撇号就能以SQL保留字作为列名（尽管这不是一个
好主意）。例如由于某些奇怪的原因，你想把某列命名为select。下列命令无法实现：```select varchar(50)```，
但```'select' varchar(50)```可以。

Q：用保留字作为列名会有什么大问题？   
A：虽然你可以这么做，但这真的是很糟糕的主意。想象一下你的查询看起来会有多么混乱，还有每次都要键入反撇号的麻
烦，还不如一开始就别把列名取为某个保留字。除此之外，select也不是一个好列名，他无法说明该列包含的数据。


### AUTO_INCREAMENT

若在列的声明中使用这个关键字，则每次执行INSERT命令来插入数据时，它都会自动给列赋予唯一的递增整数值。

```
ALTER TABLE my_contacts
    ADD contact_id INT NOT NULL AUTO_INCREMENT FIRST,
    ADD PRIMARY KEY (contact_id);
```

```
SHOW CREATE TABLE my_contacts;
# 加入自增主键后显示结果
CREATE TABLE `my_contacts` (
                               `contact_id` int(11) NOT NULL AUTO_INCREMENT,
                               `last_name` varchar(30) DEFAULT NULL,
                               `first_name` varchar(20) DEFAULT NULL,
                               `email` varchar(50) DEFAULT NULL,
                               `gender` char(1) DEFAULT NULL,
                               `birthday` date DEFAULT NULL,
                               `profession` varchar(50) DEFAULT NULL,
                               `location` varchar(50) DEFAULT NULL,
                               `status` varchar(20) DEFAULT NULL,
                               `interests` varchar(100) DEFAULT NULL,
                               `seeking` varchar(100) DEFAULT NULL,
                               PRIMARY KEY (`contact_id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=latin1;
```

当我们为表添加自增的物理主键时，表中存在的数据也会自动递增填充该字段值（自增主键必须为key```there can be only 
one auto column and it must be defined as a key```）。


```
INSERT INTO `my_contacts` (`contact_id`, `last_name`, `first_name`, `email`, `gender`, `birthday`, `profession`, `location`, `status`,
                           `interests`, `seeking`)
VALUES (NULL, 'Hamilton', 'Jamie', 'dontbother@starbuzzcoffee.com', 'F', '1964-09-10', 'System Administrator',
        'Princeton, NJ', 'married', 'RPG', 'nothing');
```
上面所示sql语句会执行成功，虽然建表时要求contact_id NOT NULL，但AUTO_INCREMENT会忽略NULL。然而，在没有
AUTO_INCREMENT的情况下，我们就会收到错误信息。



* **PRIMARY KEY** 主键。。一个或一组能识别出唯一数据行的列。
* **FIRST NORMAL FORM (1NF)** 第一范式。每个数据行均需要包含原子性数据值，而且每个数据行均需要具有唯一的识别方法。








---
<h1 id="05">05 | ALTER：改写历史</h1>

---


给表增加列，利用关键字 AFTER，让新列放在first_name列后。
```
ALTER TABLE my_contacts
    ADD phone VARCHAR(10) NULL 
    AFTER first_name;
```

我们可以使用FIRST 和 AFTER your_clumon来排列字段的位置，不过不可以使用 BEFORE your_clumon 和 LAST。
另外还有SECON、THIRD也不可选用。


**FIRST 可把phone列安置于所有其他列的前面**   

| phone | contact_id | last_name | first_name | email |   
| ------- | ------- | ------- | ------- |------- |

```
ALTER TABLE my_contacts
    ADD phone VARCHAR(10) NULL 
    FIRST;
```


**虽然有 AFTER 语法，但其实没有 BEFORE 语法，也没有LAST**   

| contact_id | last_name | first_name | email | phone |   
| ------- | ------- | ------- | ------- |------- |

```
# 不加关键字也可以
ALTER TABLE my_contacts
    ADD phone VARCHAR(10) NULL;

```

| contact_id | phone | last_name | first_name | email |   
| ------- | ------- | ------- | ------- |------- |



### 修改表

**如果改变列的类型，可能就会有遗失数据的风险**。   
如果你想改变的数据类型和原始类型不兼容，命令则不会执行，SQL软件也会抱怨语句有问题。   
但真正的惨剧可能发生在类型兼容的情况下，你的数据可能被截断。例如：从varchar(10)改为char(1)。与此类似，在数字类型上，
我们可以在各种数据类型间切换，但数据会被转换为新的类型，这是就可能丢失部分数据。




* **CHANGE** 可同时改变现有列的名称和数据类型
* **MODIFY** 修改现有列的数据类型或位置
* **ADD** 在当前表中添加一列--可自选类型
* **DROP** 从表中删除某列

### 表的改名换姓

```
CREATE TABLE `projekts`
(
    `number`            int(11) NOT NULL default '0',
    `descriptionofproj` varchar(50)      default NULL,
    `contractoronjob`   varchar(10)      default NULL
) ENGINE = MyISAM
  DEFAULT CHARSET = latin1;
```

```
ALTER TABLE projekts RENAME TO project_list;
```

重新装备列

| number | descriptionofproj | contractoronjob |
| ------- | ------- | ------- |

变更为

| proj_id | proj_desc | con_name |
| ------- | ------- | ------- |


### ALTER 和 CHANGE

```
ALTER TABLE project_list
    CHANGE COLUMN number proj_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY;
	
ALTER TABLE project_list
    CHANGE descriptionofproj proj_desc VARCHAR(50) NULL;

ALTER TABLE project_list
    CHANGE contractoronjob con_name VARCHAR(10) NULL;
```

结果展示


| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| proj\_id | int\(11\) | NO | PRI | NULL | auto\_increment |
| proj\_desc | varchar\(50\) | YES |  | NULL |  |
| con\_name | varchar\(10\) | YES |  | NULL |  |


如果只改变列的数据类型，列名维持原状，可以重复填入列名。
```
ALTER TABLE my_table
    CHANGE COLUMN my_column my_column NEWTYPE;
```
 
当然更简单的方法是，使用关键字 **MODIFY** ，它只会修改类列型而不会干涉它的名称。
```
ALTER TABLE project_list
    MODIFY COLUMN proj_desc VARCHAR(120);
```

Q：如果我想改变列的顺序呢？像```ALTER TABLE MODIFY COLUMN proj_desc AFTER con_name;```，这样做可以吗？   
A：创建表后你就无法真正地改变列的顺序了。最多只能在指定位置添加新列，然后删除旧列，但这样会丢失旧列中的所有数据。   

 正确语法
```
CHANGE [COLUMN] old_col_name column_definition
        [FIRST|AFTER col_name]
MODIFY [COLUMN] column_definition [FIRST | AFTER col_name]
```
 
 

增加多列
```
ALTER TABLE project_list
    ADD COLUMN con_phone  VARCHAR(11),
    ADD COLUMN start_date DATE,
    ADD COLUMN est_cost   DECIMAL(7, 2);
```

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| proj\_id | int\(11\) | NO | PRI | NULL | auto\_increment |
| proj\_desc | varchar\(120\) | YES |  | NULL |  |
| con\_name | varchar\(10\) | YES |  | NULL |  |
| con\_phone | varchar\(11\) | YES |  | NULL |  |
| start\_date | date | YES |  | NULL |  |
| est\_cost | decimal\(7,2\) | YES |  | NULL |  |


### 删除列

只在表中保留必要的列是一个很好的编程习惯。如果用不到某列，请把它删除（drop）。如果以后有需要，ALTER让我们可以轻松地把它添加会表中。

你的列越多，RDBMS的工作就越累，数据库所占用的空间也就越大。

一旦删除列，原本存储在该列中的一切内容都会跟着被删除。

```
ALTER TABLE project_list
    DROP COLUMN start_date;
```

 表 hooptie
```
CREATE TABLE `hooptie`
(
    `color`   varchar(20)  default NULL,
    `year`    varchar(4)   default NULL,
    `make`    varchar(20)  default NULL,
    `mo`      varchar(20)  default NULL,
    `howmuch` float(10, 3) default NULL
) ENGINE = MyISAM
  DEFAULT CHARSET = latin1;
```

```
INSERT INTO `hooptie` (`color`,`year`,`make`,`mo`,`howmuch`) VALUES ('silver','1998','Porsche','Boxter','17992.539');
INSERT INTO `hooptie` (`color`,`year`,`make`,`mo`,`howmuch`) VALUES (NULL,'2000','Jaguar','XJ','15995.000');
INSERT INTO `hooptie` (`color`,`year`,`make`,`mo`,`howmuch`) VALUES ('red','2002','Cadillac','Escalade','40215.898');
```

| color | year | make | mo | howmuch |
| :--- | :--- | :--- | :--- | :--- |
| silver | 1998 | Porsche | Boxter | 17992.54 |
| NULL | 2000 | Jaguar | XJ | 15995 |
| red | 2002 | Cadillac | Escalade | 40215.9 |

修改为 car_table

| car\_id | VIN | make | model | year | color | price |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | NULL | Porsche | Boxter | 1998 | silver | 17992.54 |
| 2 | NULL | Jaguar | XJ | 2000 | NULL | 15995.00 |
| 3 | NULL | Cadillac | Escalade | 2002 | red | 40215.90 |



 执行语句
```
ALTER TABLE hooptie RENAME TO car_table,
    ADD COLUMN car_id           Int NOT NULL AUTO_iNCREMENT PRIMARY KEY FIRST,
    ADD COLUMN VIN              VARCHAR(16) AFTER car_id,
    CHANGE COLUMN mo model      VARCHAR(20),
    MODIFY COLUMN color varchar(20) AFTER model,
    MODIFY COLUMN year varchar(4) AFTER model,
    CHANGE COLUMN howmuch price DECIMAL(7, 2);
```


Q：之前你说过，MODIFY无法重新排列列的顺序。但是我的SQL软件工具却能让我重新排列它们。它是怎么办到的？
A：其实你的软件在背后执行了非常多的命令。它把我们要移除的列的内容暂时复制到临时表中，然后去除你要移除的列，
再用ALTER创建于旧列同名的新列，并放再我们指定的位置，而后把临时表的内容复制到新列中，最后再删除临时表。   

建议再SELECT时用任何顺序排列列。

Q：所以说，只有添加列时才是调整顺序的好时机吗？
A：没错。最好在设计表时就已构思好各个列的最佳顺序。

Q：如果我已经创建了主键，然后又意外地想改用另一列呢？可以只移除主键的设置而不改变其中的数据吗？
A：可以，而且很简单。

```
ALTER TABLE your_table DROP PRIMARY KEY;
```
但是该列必须不能是 AUTO_INCREMENT修饰的列。因为 PRIMARY KEY 和 AUTO_INCREMENT必须一起使用。
```
there can be only one auto column and it must be defined as a key
```

每个表中只有一列可以加上AUTO_INCREMEN，该列必须为整数类型而且不能为NULL，必须为PRIMARY KEY。

### 寻找模式

```
SELECT location FROM my_contacts;
```

| location |
| :--- |
| Palo Alto, CA |
| San Francisco, CA |
| San Diego, CA |

增加列，把location字段值拆分为state、city
```
ALTER TABLE my_contacts
    ADD COLUMN city  VARCHAR(50),
    ADD COLUMN state VARCHAR(50);
```

| location | city | state |
| :--- | :--- | :--- |
| Palo Alto, CA | Palo Alto |  CA |
| San Francisco, CA | San Francisco |  CA |
| San Diego, CA | San Diego |  CA |
| Dallas, TX | Dallas |  TX |

执行语句( 没有where条件，会遍历整个表，每次取一条数据套用函数并更新)
```
UPDATE acc_adapter.my_contacts
SET city  = SUBSTRING_INDEX(location, ',', 1),
    state = SUBSTRING_INDEX(location, ',', -1);
```

**MySQL中的错误语句，mssql和Oracle不会出现此问题。**

```
UPDATE acc_adapter.my_contacts
             SET city  = (SELECT SUBSTRING_INDEX(location, ',', 1) FROM acc_adapter.my_contacts WHERE 1 = 1),
                 state = (SELECT SUBSTRING_INDEX(location, ',', -1) FROM acc_adapter.my_contacts WHERE 1 = 1)
             WHERE 1 = 1;
[2021-05-19 10:45:06] [HY000][1093] You can't specify target table 'my_contacts' for update in FROM clause
```
意思是说，不能先select出同一表中的某些值，再update这个表(在同一语句中)。   
可将select出的结果再通过中间表select一遍，这样就规避了错误。



### 一些便利的字符串函数

字符串函数不会改变存储在表中的内容，它只是把字符串修改后的模样当成查询结果返回。

**SELECT最后两个字符**

RIGHT() 和 LEFT() 可从列中选出指定数量的字符串（right从右侧开始，left从左侧开始）。

```
SELECT RIGHT(location, 2) FROM my_contacts;
```

**SELECT逗号前的所有内容**

SUBSTRING_INDEX()则可截取部分值，也称为子字符串。

SUBSTRING_INDEX(s, delimiter, number)
* 返回从字符串 s 的第 number 个出现的分隔符 delimiter 之后的子串（如果s中没有分隔符，则返回整个字符串s）。	
* 如果 number 是正数，返回第 number 个字符左边的字符串。
* 如果 number 是负数，返回第(number 的绝对值(从右边数))个字符右边的字符串。

```
SELECT SUBSTRING_INDEX('ab','',1) -- a	
SELECT SUBSTRING_INDEX('ab','',-1) -- b		
SELECT SUBSTRING_INDEX(SUBSTRING_INDEX('abcde','',3),'',-1) -- c
```



---
<h1 id="06">06 | SELECT进阶：以新视角看你的数据</h1>

---

内容提示：更精确的WHERE、数据排序、归组、套用数学运算。


### 使用 CASE 表达式来 UPDATE

```
CREATE TABLE `movie_table`
(
    `movie_id`  int(11)     NOT NULL auto_increment,
    `title`     varchar(50) NOT NULL,
    `rating`    varchar(5)  NOT NULL,
    `category`  varchar(10) NOT NULL,
    `purchased` date        NOT NULL,
    PRIMARY KEY (`movie_id`)
) ENGINE = MyISAM
  AUTO_INCREMENT = 94
  DEFAULT CHARSET = utf8;
```

```
UPDATE movie_table
SET category =
        CASE
            WHEN drama = 'T' THEN 'drama'
            WHEN comedy = 'T' THEN 'comedy'
            WHEN action = 'T' THEN 'action'
            WHEN gore = 'T' THEN 'horror'
            WHEN scifi = 'T' THEN 'scifi'
            WHEN for_kids = 'T' THEN 'family'
            WHEN cartoon = 'T' THEN 'family'
            ELSE 'misc'
        END;
```


一个条件表达式可以包含许多部分：在 WHERE 子句中加上 AND ，检查影片是否即为 cartoon 又为'G'级。如果两项都
符合就归为'family'类。
```
UPDATE movie_table
SET category =
        CASE
            WHEN drama = 'T' THEN 'drama'
            WHEN comedy = 'T' THEN 'comedy'
            WHEN action = 'T' THEN 'action'
            WHEN gore = 'T' THEN 'horror'
            WHEN scifi = 'T' THEN 'scifi'
            WHEN for_kids = 'T' THEN 'family'
            WHEN cartoon = 'T' AND rating = 'G' THEN 'family'
            ELSE 'misc'
        END;
```

Q：如果我只想对部分列套用 CASE 表达式，应该怎么做呢？例如，只想对部分符合 categroy = 'misc'的套用 CASE，
可以加上 WHERE 吗？   
A：是的，可以在关键字 END 后加上 WHERE 子句。这样，CASE 就只会套用在符合 WHERE 条件的列上。

Q：CASE 表达式可以搭配 UPDATE 以外的语句吗？   
A：可以。CASE 表达式可以搭配 SELECT、INSERT、DELETE、UPDATE。




### 先分组，然后组内排序

我们需要根据分类选出影片，并按字母顺序排列分类中的影片。

**SQL的排序规则 ORDER BY （可能会因为RDBMS软件不同而有差异）**
* 非字母字符出现在数字的前面或后面
* 数字出现在字母的前面
* NULL 出现在数字的前面
* NULL 出现在字母的前面
* 大写字母出现在小写字母的前面
* "A 1" 出现在"A1"的前面

**按多列排序**
查询结果的排序依据不局限于使用一列或两列。我们可以利用所有需要的列来排序所有结果。

```
SELECT *
FROM movie_table
ORDER BY category, purchased, title;
```

默认情况下，SQL根据ORDER BY 指定的列中的升序ASC（ASCENDING）排列查询结果（A-Z，1到99,999，时间从早到晚）。

我们也可以针对不同的字段使用不同的排序方式
```
SELECT *
FROM movie_table
ORDER BY category ASC, purchased DESC;
```

显示结果

| movie\_id | title | rating | category | purchased |
| :--- | :--- | :--- | :--- | :--- |
| 86 | Head First Rules | R | action | 2003-04-19 |
| 84 | Greg: The Untold Story | PG | action | 2001-02-05 |
| 90 | Take it Back | R | comedy | 2001-02-05 |
| 89 | Shiny Things, The | PG | drama | 2002-03-06 |
| 87 | A Rat named Darcy | G | family | 2003-04-19 |
| 83 | Big Advenure | G | family | 2002-03-06 |

Q：我记得DESC是“表说明”（DESCRIPTION）的意思。你确定它能用在ORDER字句中？   
A：我，确定。一切都跟上下文有关。当DESC用在表名前，例如DESC movie_table；查询结果就是表的说明，此时DESC是DESCRIBE的缩写。
但DESC出现在ORDER BY子句时，它则代表DESCENDING（降序），是一种排序方式。

Q：我可以使用完整的DESCRIBE或DESCENDING，以免混淆吗？   
A：可以使用DESCRIBE，但没有DESCENDING这个关键词。   

**利用 GROUP BY 完成分组加总**

统计每个女孩的销售总量，找出冠军
```
CREATE TABLE cookie_sales
(
    ID         int(11)       NOT NULL auto_increment,
    first_name varchar(20)   NOT NULL,
    sales      decimal(4, 2) NOT NULL,
    sale_date  date          NOT NULL,
    PRIMARY KEY (ID)
);
```

```
SELECT first_name, SUM(sales)
FROM cookie_sales
GROUP BY first_name
ORDER BY SUM(sales);
```

| first\_name | SUM\(sales\) |
| :--- | :--- |
| Lindsey | 81.08 |
| Ashley | 96.03 |
| Nicole | 98.23 |
| Britney | 107.91 |


**AVG 搭配 GROUP BY**

```
SELECT first_name, AVG(sales)
FROM cookie_sales
GROUP BY first_name;
```

| first\_name | AVG\(sales\) |
| :--- | :--- |
| Lindsey | 11.582857 |
| Nicole | 14.032857 |
| Britney | 15.415714 |
| Ashley | 13.718571 |


MAX 和 MIN

```
SELECT first_name, MAX(sales), MIN(sales)
FROM cookie_sales
GROUP BY first_name;
```

| first\_name | MAX\(sales\) | MIN\(sales\) |
| :--- | :--- | :--- |
| Lindsey | 32.02 | 0.00 |
| Nicole | 31.99 | 0.00 |
| Britney | 43.21 | 2.58 |
| Ashley | 26.82 | 0.00 |


**COUNT**

COUNT 返回 sales_date 列中的行数。如果数据只是NULL，则不纳入计算。

```
SELECT first_name, COUNT(sales)
FROM cookie_sales;
```

找出卖出饼干的天数最多的女孩
```
SELECT first_name, COUNT(DISTINCT sale_date)
FROM cookie_sales
WHERE sales <> 0
GROUP BY first_name;
```

| first\_name | COUNT\(DISTINCT sale\_date\) |
| :--- | :--- |
| Ashley | 6 |
| Britney | 7 |
| Lindsey | 6 |
| Nicole | 6 |

**NULL 代表此处无值，而不是此值为0**

### LIMIT 查询结果的数量

LIMIT 指定呈现的结果为2行
```
SELECT first_name, SUM(sales)
FROM cookie_sales
GROUP BY first_name
ORDER BY SUM(sales)
LIMIT 2;
```

LIMIT ，只限第二名出现

```
SELECT first_name, SUM(sales)
FROM cookie_sales
GROUP BY first_name
ORDER BY SUM(sales) DESC
LIMIT 1,1;
```

| first\_name | SUM\(sales\) |
| :--- | :--- |
| Nicole | 98.23 |



```
SELECT first_name, SUM(sales)
FROM cookie_sales
GROUP BY first_name
ORDER BY SUM(sales) DESC
LIMIT 0,4;
```

**0，代表查询结果的起始处（SQL从0开始计算）；4代表返回查询结果的数量。**

| first\_name | SUM\(sales\) |
| :--- | :--- |
| Britney | 107.91 |
| Nicole | 98.23 |
| Ashley | 96.03 |
| Lindsey | 81.08 |






---
<h1 id="07">07 | 多张表的数据库设计；拓展你的表</h1>

---

到了某个时候，只有一张表就不够了。数据变得越来越复杂，你所使用的唯一 一张表实在装不下了。表里充满了
多余的数据，既浪费存储空间，又会拖慢查询的速度。

**将兴趣文本字符串拆分为多个兴趣列。**

使用ALTER和SUBSTRING_INDEX函数把表修改成具有如下列。

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| status | varchar\(50\) | YES |  | NULL |  |
| seeking | varchar\(100\) | YES |  | NULL |  |
| city | varchar\(50\) | YES |  | NULL |  |
| state | varchar\(50\) | YES |  | NULL |  |
| interests1 | varchar\(50\) | YES |  | NULL |  |
| interests2 | varchar\(50\) | YES |  | NULL |  |
| interests3 | varchar\(50\) | YES |  | NULL |  |
| interests4 | varchar\(50\) | YES |  | NULL |  |

具体步骤：
```
# 首先创建新的列：
ALTER TABLE my_contacts
    ADD COLUMN interests1 varchar(50) null,
    ADD COLUMN interests2 varchar(50) null,
    ADD COLUMN interests3 varchar(50) null,
    ADD COLUMN interests4 varchar(50) null;

# 然后把第一项兴趣移至新的interests1列。
UPDATE acc_adapter.my_contacts
SET interests1 = SUBSTRING_INDEX(interests, ',', 1);

# 接下来从原始interests字段中移除第一项兴趣
UPDATE acc_adapter.my_contacts
SET interests = TRIM(RIGHT(interests, (LENGTH(interests) - LENGTH(interests1) - 1)));

# 对interests列的剩余部分兴趣重复上述步骤
UPDATE acc_adapter.my_contacts
SET interests2 = SUBSTRING_INDEX(interests, ',', 1);

UPDATE acc_adapter.my_contacts
SET interests = TRIM(RIGHT(interests, (LENGTH(interests) - LENGTH(interests2) - 1)));

UPDATE acc_adapter.my_contacts
SET interests3 = SUBSTRING_INDEX(interests, ',', 1);

UPDATE acc_adapter.my_contacts
SET interests = TRIM(RIGHT(interests, (LENGTH(interests) - LENGTH(interests3) - 1)));

# 在最后一列中，我们只会得到单一值
UPDATE acc_adapter.my_contacts
SET interests4 = interests;

# 现在终于可以完全删除interests列了，我们也可以将它重新命名为interests4，不需要多加一次ADD COLUMN(假设只有四项兴趣)
ALTER TABLE my_contacts
    DROP COLUMN interests;
```











































TODO p323







---
<h1 id="08">08 | 联接与多张表的操作：不能单独存在吗？</h1>

---





