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

对数据库内的数据描述（列和表），以及任何相关对象和各种连接方式的描述就称为**SCHEMA**，模式。

### 外键

**外键**是表中的某一列，它引用到另一个表的主键。

* 外键可能与它引用的主键名称不同。
* 外键使用的主键也被称为父键（parent key）
* 主键所在的表又被称为父表（parent table）
* 外键能用于确认一张表中的行与另一张表中的行相对应。
* 外键的值可以是NULL，即使主键值不可为NULL。
* 外键值不需要唯一 - -事实上，外键通常都没有唯一性。

Q：我们知道外键可以让我连接两张表。但是如果外键是NULL，它有什么作用吗？有办法确认外键连接至父键了吗？   
A：**外键为NULL，表示在父表中没有相符的主键。** 但我们可以确认外键包含有意义、已存储在父表中的值，
请通过**约束（constrant）**来实现。


### 外键约束

创建一张表并加上可以作为外键的列虽然很简单，但除非你利用CREATE或ALTER语句来指定外键，否则不算是真的外键。
创建在结构内的键被称为**约束（constraint）**。

创建外键作为表的约束提供了明确的优势。如果违反了规则，约束会阻止我们意外破坏表。

插入外键列的只必须已经存在于父表的来源列中，这是**引用完整性（referntial integrity）**。

你可以使用外键来引用父表中的某个唯一的值。外键不一定必须是父表的主键，但必须有唯一性。

### 创建带有外键的表

```
CREATE TABLE interests
(
    int_id     INT         NOT NULL AUTO_INCREMENT PRIMARY KEY,
    interest   VARCHAR(50) NOT NULL,
    # 创建外键就和创建索引列一样：把外键列设定为INT 与 NOT NULL。
    contact_id INT         NOT NULL,
    # 这部分称为CONSTRAINT，它的命名方式能告诉我们键的来源（my_contacts）、键的名称（ocntact_id），
    # 还能说明它是一个外键（fk）。
    # 如果以后解除约束，也还要用这个名称解除。本行为可选，但最好养成使用它的习惯。
    CONSTRAINT my_contacts_cont_contant_id_fk
        # 括号中的列名就代表外键。可以随意命名
        FOREIGN KEY (contact_id)
            # 这部分指定外键的来源，还有外键列在另一张表中的名称。
            REFERENCES my_contacts (contact_id)
);
```

表结构

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| int\_id | int\(11\) | NO | PRI | NULL | auto\_increment |
| interest | varchar\(50\) | NO |  | NULL |  |
| contact\_id | int\(11\) | NO | MUL | NULL |  |

MUL 表示这一列可以存储多个相同的值，它也是追踪每个cocntact_id拥有什么兴趣的关键。

约束有助于加强两张表之间的连接。因为外键约束能确保引用完整性（换句话说，如果表中的某行有外键，
约束能确保该行通过外键与另一张表中的某一行对应）。

如果数据行具有主键，而且他的主键是其它表的外键时，删除数据或修改主键值，必须先移除外键行。

除了外键约束，关键字 UNIQUE 也被视为一种约束。此外还有一种MySQL不支持的约束- - CHECK（检查）约束。


### 表间的关系

一对一、一对多、多对多。

找出数据所属的模式后，设计多张表的关系- - 设计**数据库模式**（schema），也就变得简单了。


### 数据模式： 一对一

一对一：父表只有一行与子表的某行相关。

假设有A、B两个表，A表的某条记录在B表中最多只能有一条相对应的记录。

**使用一对一表的时机**

通常，把一对一的的数据留在主表更合理，但也有适合把某些列拉出来的时候：
1. 抽出数据或许能让你写出更快速的查询。例如，如果大多数时候你只需要查询SSN，就可以查询较小的SSN表。
2. 如果有列包含还不知道的值，可以单独存储这一列，以免主要表中出现NULL。
3. 我们可能希望某些数据不要太常被访问。隔离这些数据即可管制访问次数。以员工表为例，它们的薪资信息最好存为另一张表。
4. 如果有一大块数据，例如BLOB类型，这段数据或许存为另一张表会更好。

### 数据模式： 一对多

一对多：A表中的某一条记录可以对应到B表中多条记录，但B表中的某一条记录只能对应到A表中的某一条记录。

### 数据模式： 多对多

表 my_contacts

| Field |
| :--- |
| contact_id |
| last_name |
| first_name |
| phone |
| email |
| gender |
| birthday |
| profession |
| location |
| status |
| seeking |
| city |
| state |


表 contact_interest

| Field |
| :--- |
| contact_id |
| interest_id |

表 interests

| Field |
| :--- |
| interest_id |
| interest |


my_contacts 与 interests 表彼此间具有多对多关系。此时，在两个多对多的表之间需要一个中间桥梁来存储两个相关
表的主键，从而把关系简化为一对多，这个中间桥梁就是所谓的 **junction table（连接表）**。

Q：遇到多对多关系的时候，一定要创建中间表吗？   
A：是的，应该如此。如果两个表具有多对多关系，结果只会造成重复组违反了第一范式。

Q：把表改造成上述形式后有什么优点？我可以把所有兴趣放在一个只有contact_id与interest_name 列的表中。
的确，数据重复，但真的会有什么问题吗？   
A：当你使用下一章的联接（join）查询多张表时就会体验到优点了。但是根据使用数据的方式，重复数据也可能有用处，
像遇到重点在于多对多关系的表，而不是两张表中的数据的时候。

Q：如果我就是不在意数据重复呢？   
A：Junction table 有助于保持数据的完整性。如果必须删除某个存储在my_contacts表中的联络人时、连接让我们不需
分心注意interests表，只需管理contact_interest表。若没有分开的表，你极有可能意外移除错误的记录。这种方式比较
安全。  还有，在更新数据时，没有重复数据能让工作更顺利。假设我们不小心拼错了某个冷门兴趣名，例如拼错了"spelunking"
（洞穴探险）。当你修正它时，只要修给interests表中的一行记录，而不需要修改contact_interest或my_contacts表。


### 第一范式（INF）

* 规则一：数据列只包含具有原子性的值
* 规则二：没有重复的数据组

### 组合键

**组合键**就是由多个数据列构成的主键，组合各列后形成具有唯一性的键。

### 函数依赖（function dependency）

当某列的数据必须随着另一列的数据的改变而改变时，表示第一列**函数依赖**于第二列。

### 速记符号

快速表示函数依赖的方式是：   
T.x -> T.y   
可以解释成“在关系表 T 中，y 列函数依赖于 x 列”。基本上，从右读到左就是解读依赖性的方式。

### 部分函数依赖（paartial functional dependency）

**部分函数依赖**是指，非主键的列依赖于组合主键的某个部分（但不是完全依赖于组合主键）。

### 传递函数依赖（transitive dependency）

如果改变任何非键列可能造成其他列的改变(任何非键列与另一个非键列有关联)，即为传递依赖。



### 人造键或自然键（synthetic or natural key）

TODO

本书主要采用单一人造主键，用于维持语法的简单，让大家能学到该学的概念，又不会因为实现约束而受困。

### 第二范式（2NF）

在表中加入主键列有助于达成2NF。因为第二范式的重点就是**表的主键如何与其他数据产生关系**。

* 规则一：先符合1NF
* 规则二：没有部分函数依赖性。

任何具有人工主键且没有组合主键的表都符合2NF。


### 第三范式（3NF）

规则一：符合2NF   
规则二：没有传递函数依赖性


### SQL工具包

- Schema 

  - 数据库模式。描述数据库中的数据、其他相关对象，以及这些对象相互连接的方式。

- One-to-One relationship

  - 一对一关系。父表中的一行记录只与子表中的一行记录相关联。

- One-to-Many relationship

  - 一对多关系。一张表中的一行记录可能与另一张表中的多行记录相关联，但后一张表中的任一行记
录只会与前一张表中的一行记录相关联。

- Many-to-Many relationship

  - 多对多关系。两个通过 junction table 连接的表。让一张表中的多行记录能与另一张表中的多
行记录相关联，反之亦然。

- First normal form (1NF)

  - 第一范式。列中只包含原子性数据，而且列没有重复的数据组。

- Transitive functional dependency

  - 传递函数依赖。指任何非键列依赖于另一个非键。

- Second normal form (2NF)

  - 第二范式。表必须先符合1NF，同时不能包含部分函数依赖，才算满足2NF。

- Third normal form (3NF)

  - 第三范式。表必须先符合2NF，同时不可包含可传递函数依赖。

- Foreign key

  - 外键。引用其他表的主键列。

- Composite key

  - 组合键。由多个列构成的主键，这些列需形成唯一的键值。





---
<h1 id="08">08 | 联接与多张表的操作：不能单独存在吗？</h1>

---

### 利用 SELECT 语句把内容直接填入新表的三种方式

1. CREATE TABLE，然后利用 SELECT 进行 INSERT。同时（几乎同时啦）CREATE、SELECT、INSERT
```
CREATE TABLE profession
(
    id         INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    profession varchar(20)
);
INSERT INTO profession (profession)
SELECT profession
FROM my_contacts
GROUP BY profession
ORDER BY profession;
```

2. 利用 SELECT 进行 CREATE TABLE，然后 ALTER 以添加主键。同一时间CREATE、SELECT、INSERT。

```
CREATE TABLE profession AS
SELECT profession
FROM my_contacts
GROUP BY profession
ORDER BY profession;

ALTER TABLE profession
    ADD COLUMN id INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY FIRST;
```

出现错误提示：
```
Statement violates GTID consistency: CREATE TABLE ... SELECT.
```

3. CREATE TABLE 的同时设置主键并利用 SELECT 填入数据

创建profession表的同时设置主键列以及另一个VARCHAR类型的列来存储职业，同时还要填入SELECT 的查询结果。
SQL具有 AUTO_INCREMENT 功能，所以RDBMS知道ID列需要自动填入，因此只剩一列，也就是SELECT的数据应该填入的地方。

```
CREATE TABLE profession
(
    id         INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    profession varchar(20)
) AS
SELECT profession
FROM my_contacts
GROUP BY profession
ORDER BY profession;
```

出现错误提示：
```
Statement violates GTID consistency: CREATE TABLE ... SELECT.
```

错误说明：
```
1、情况描述
在执行sql：create table 表A  as select * from 表B时，发现sql执行后，并未生成新的表，
而是提示Statement violates GTID consistency: CREATE TABLE ... SELECT.

2、问题分析
MySQL5.6及以上的版本，开启了 enforce_gtid_consistency=true 功能导致的，MySQL官方解释
说当启用 enforce_gtid_consistency 功能的时候，MySQL只允许能够保障事务安全，并且能够被
日志记录的SQL语句被执行，像create table … select 和 create temporarytable语句，以及
同时更新事务表和非事务表的SQL语句或事务都不允许执行。

3、解决方法  

方法一（推荐）：
修改 ：SET @@GLOBAL.ENFORCE_GTID_CONSISTENCY = off;

配置文件中 ：ENFORCE_GTID_CONSISTENCY = off;

方法二：
create table 表A as select 表B 的方式会拆分成两部分。

create table 表A like 表B ;
insert into 表A select *from 表B ;

```










### AS 到底是怎么一回事？

AS 能把SELECT的查询结果填入新表中。我们在上文第二和第三个范例中使用AS时，其实是要求软件把来自my_contacts表的内容当成
SELECT的查询结果，并把结果值存入新建的profession表中。

如果不指定新表具有带有新名称的两列，AS只会创建一列，且该列的名称及数据类型与SELECT的查询结果相同。


### 列的别名

```
CREATE TABLE profession
(
    id      INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    mc_prof varchar(20)
) AS
SELECT profession AS mc_prof
FROM my_contacts
GROUP BY mc_prof
ORDER BY mc_prof;
```

这个查询与原先的查询效果完全一样，但因为有别名，所以它更容易理解。

虽然这两个查询有一点小小的不同。但所有的查询都以表的行是返回。别名改变了查询结果中的列名，但并未改变来源列的名称。别名
只是临时的。

但因为指定了新表有两列-- 主键和职业列相当于覆盖了原始查询结果，所以新表还是会有名为profession的列，而非mc_prof。

### 表的别名，谁会需要？

联接（join）领域。

表的别名又称 correlation name （相关名称）。

对于表和列的别名，我们可以省略关键字AS。



###  交叉联接

本书称之为交叉联接（cross join）,它也有别的名字，例如笛卡儿积、交叉积......还有最奇怪的“没有联接”（no join）。

假设有一个存储男孩姓名的表，一个存储玩具的表。

```
CREATE TABLE `boys` (
                        `boy_id` int(11) default NULL,
                        `boy` varchar(20) default NULL,
                        `toy_id` int(11) default NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1;
```

```
CREATE TABLE `toys` (
                        `toy_id` int(11) default NULL,
                        `toy` varchar(20) default NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1;
```




boys

| boy\_id | boy |
| :--- | :--- |
| 1 | Davey |
| 2 | Bobby |
| 3 | Beaver |
| 4 | Richie |


toys

| toy\_id | toy |
| :--- | :--- |
| 1 | hula hoop |
| 2 | balsa glider |
| 3 | toy soldiers |
| 4 | harmonica |
| 5 | baseball cards |


下列同时查询玩具表的toy列与男孩表的boy列，这个方法的查询结果会是交叉联接。


```
SELECT t.toy, b.boy
FROM toys t
         CROSS JOIN boys b;
```

CROSS JOIN 返回两张表的每一行相乘结果。即交叉联接把每一张表的每一个值都与第二张表的每一个值配成对。

| toy | boy |
| :--- | :--- |
| hula hoop | Davey |
| hula hoop | Bobby |
| hula hoop | Beaver |
| hula hoop | Richie |
| hula hoop | Johnny |
| hula hoop | Billy |
| balsa glider | Davey |
| balsa glider | Bobby |
| balsa glider | Beaver |
| balsa glider | Richie |
| ...... | ...... |

本例联接出20条记录。5个玩具乘以4个男孩的结果呈现了所有可能的组合。

因为 toys.toy 的查询结果比较多，所以结果呈现如上表所示的组。如果男孩有5个，玩具只有4中，则会以男孩姓名为划分组的依据（如下表）。

```
SELECT t.toy, b.boy
FROM toys t
         CROSS JOIN boys b;
```

| toy | boy |
| :--- | :--- |
| hula hoop | Davey |
| balsa glider | Davey |
| toy soldiers | Davey |
| hula hoop | Bobby |
| balsa glider | Bobby |
| toy soldiers | Bobby |
| hula hoop | Beaver |
| balsa glider | Beaver |
| toy soldiers | Beaver |

Q：我为什么需要交叉联接？   
A：知道这一点很重要，因为当我们乱玩联接时可能意外造成交叉联接。知道交叉联接的存在有益于找出修正联接的方式。这种事情有时真的
会发生。还有，交叉联接有时可用于检测 RDBMS 软件及其配置的运行速度。运行交叉联接所需的时间可以轻易地检测与比较速度慢的查询。

Q：如下所示。假设改用 SELECT * 写查询，会有什么不同？   
```SELECT * FROM toys CROSS JOIN boys;```   
A：你可以动手试试看。不过还是会得出20行。只不过会包含4个数据列。   

| toy\_id | toy | boy\_id | boy |
| :--- | :--- | :--- | :--- |
| 1 | hula hoop | 1 | Davey |
| 2 | balsa glider | 1 | Davey |
| 3 | toy soldiers | 1 | Davey |
| 1 | hula hoop | 2 | Bobby |
| 2 | balsa glider | 2 | Bobby |
| 3 | toy soldiers | 2 | Bobby |
| 1 | hula hoop | 3 | Beaver |
| ...... | ...... | ...... |...... |

Q：如果对两个很大的表做了交叉联接，会发生什么事？   
A：查询结果将会非常的庞大。最好别对数据量大的表进行交叉联接，否则会因为返回的数据过多而让机器冒着停滞不动的风险。

Q：这个查询还有其他同义语法吗？   
A：当然有！ **CROSS JOIN 可以省略不写**，只用逗代替，就像这样：   
```
SELECT t.toy, b.boy FROM toys t, boys b;
```

Q：**我听说过“内联接”与“外联接”这两个词，交叉联接是相同的东西吗**？   
A：交叉联接是内联接的一种。内联接基本上就是通过查询中的条件移除了某些结果数据行后的交叉联接。



###  内联接：相等联接（equijoin）

**内联接就是通过查询中的条件移除了某些结果数据行后的交叉联接**

```
SELECT b.boy, b.boy FROM boys t, boys b;
```
结果展示（相当于两个表，表1（n 行）的每一行与表2（m 行）的每一行组合（Cn1*Cm1））：

| boy | boy |
| :--- | :--- |
| Davey | Davey |
| Davey | Davey |
| Davey | Davey |
| Davey | Davey |
| Davey | Davey |
| Davey | Davey |
| Bobby | Bobby |
| Bobby | Bobby |
| Bobby | Bobby |
| Bobby | Bobby |
| Bobby | Bobby |


```
SELECT mc.last_name, mc.first_name, p.profession
FROM my_contacts AS mc
         INNER JOIN profession AS p ON mc.prof_id = p.id;
		 
SELECT boys.boy, toys.toy
FROM boys
         INNER JOIN toys ON boys.toy_id = toys.toy_id;
```

**INNER JOIN** 利用条件判断中的比较运算符结合两张表的记录。只有联接记录符合条件时才会返回列。

其中关键字 ON 可以改为 WHERE ，条件式里可采用任何一个比较运算符。


###  内联接：不等联接（non-equijoin）

每个男孩没有的玩具(或许生日的时候会有用)

```
SELECT boys.boy, toys.toy
FROM boys
         INNER JOIN toys ON boys.toy_id <> toys.toy_id
ORDER BY boys.boy;
```


| boy | toy |
| :--- | :--- |
| Beaver | toy soldiers |
| Beaver | tinker toys |
| Beaver | harmonica |
| Billy | baseball cards |
| Billy | harmonica |

前3行为Beaver没有的玩具。


###  内联接：自然联接（naturaljoin）

**自然联接**只有在联接的列在两张表中的名称都相同时才会有用。

**NATURAL JOIN 利用相同列名的内联接**

我们想知道每个男孩拥有什么玩具。自然联接会识别出每个表里的相同名称（toy_id）并返回相符的记录。

```
SELECT boys.boy, toys.toy
FROM boys
         NATURAL JOIN toys;
```


练习

| table my_contacts Field | table profession Field | table zip_code Field | table status Field |
| :--- |:--- |:--- |:--- |
| last\_name |prof_id |zip_code |status_id |
| first\_name | profession |city |status |
| email ||state ||
| gender |
| ...... |
| prof\_id |
| zip_code |
| status_id |


返回my_contacts表中每个人的电子邮件地址与职业的查询
```
SELECT mc.email, p.profession
FROM my_contacts mc
         NATURAL JOIN profession p;
```

返回my_contacts表中每个人的姓、名与他们没有的状态的查询
```
SELECT mc.last_name, mc.first_name, s.status
FROM my_contacts mc
         INNER JOIN satatus s ON mc.status_id <> s.status_id;
```

返回my_contacts表中每个人的姓、名与所在位置（州名）的查询
```
SELECT mc.last_name, mc.first_name, z.state
FROM my_contacts mc
         NATURAL JOIN zip_code z;
```

简单说明：

- 返回两张表里联接列内容不符合条件的所有记录
  - 不等联接（non-equijoin）
  - 内联接（inner join）
- 联接表的顺序对我来说很重要
  - 外联接（outer join）
- 返回两张表里联接列内容符合条件的所有记录，而且使用关键字ON
  - 相等联接（equijoin）
  - 内联接（inner join）
- 能结合两个共享相同列名的表
  - 自然联接（natural join）
- 可以返回等于两张表的数据行的乘积记录
  - 交叉联接（cross join）
  - 笛卡尔联接（cartesian join）
  - 叉积（cross product）
- 返回所有可能的行，而且没有任何条件
  - 笛卡尔联接（cartesian join）
  - 叉积（cross product）
- 利用条件结合两张表
  - 相等联接（equijoin）
  - 不等联接（non-equijoin）
  - 内联接（inner join）


练习：

1. 使用不同联接方式涉及两个查询，取得my_contacts 与contact_interests里相符的记录

```
SELECT mc.first_name, mc.last_name, ci.interest_id
FROM my_contacts mc
         INNER JOIN contact_interest ci ON mc.contact_id = ci.contact_id;
		 
SELECT mc.first_name, mc.last_name, ci.interest_id
FROM my_contacts mc
         NATURAL JOIN contact_interest;
```

2. 设计一个查询，返回 contact_seeking 与 seeking 所有可能的合并结果

```
SELECT *
FROM contact_seeking
         CROSS JOIN seeking;
		 
SELECT * FROM contact_seeking, seeking;
```
3. 列出my_contacts表中每个人的职业，但职业不能重复列出，而且要按字母顺序排列

```
SELECT p.profession
FROM my_contacts mc
         INNER JOIN profession p ON mc.prof_id = p.id
GROUP BY p.profession
ORDER BY p.profession;
```


### SQL工具包

- **INNER JOIN**
  - 内联接。任何使用条件结合来自两张表的记录的联接。
- **NATURAL JOIN**
  - 自然联接不使用“ON”子句的内联接。只有在联接的两张表中有同名列时才能顺利运作。
- **EQUIJOIN**与**NON-EQUIJOIN**
  - 相等联接与不等联接。两者均为内联接的一种。相等联接返回相等的行，不等联接则返回不相等的行。
- **CROSS JOIN**
  - 交叉联接。返回一张表的每一行与另一张表的每一行所有可能的搭配结果。其他常见名称还包括笛卡尔联接（CARTESIAN JOIN）与 NO JOIN。
- **COMMA JOIN**
  - 与 CROSS JOIN 相同。只不过以逗号取代关键字 CROSS JOIN。







---
<h1 id="09">09 | 子查询：查询中的查询</h1>

---

有时要问数据库的问题不止一个，或者需要把甲查询的结果作为乙查询的输入，这时就需要使用子查询。
子查询有助于避免数据重复，让查询更加动态灵活。

任何子查询能实现的事情都能以相同类型的联接来实现。

**子查询，是被另一个查询包围的查询，也可称为内层查询。**

**子查询只不过是查询里的查询**

外部的查询称为**包含查询（containing query）**或**外层查询**。内部查询就是内层查询、或称为**子查询**。

```
SELECT some_column, another_column
FROM table
WHERE column = (SELECT column FROM table);
```

因为查询里使用了= 运算符，所以子查询只会返回**单一值，特定行和列的交叉点**（有时称为cell，但SQL称之为标量值、scalar value）,
这一个值将是WHERE子句中比对数据列的条件。


子查询示例：

检查联络人清单中是否存储了符合职务需求的人才（因为表与my_contacts表有一对一关系，所以使用自然联接）
```
SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc
         NATURAL JOIN my_contacts AS mc
WHERE jc.title IN ('Cook', 'Hairdresser', 'Waiter', 'Web Designer', 'Web Developer');

SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc
         NATURAL JOIN my_contacts AS mc
WHERE jc.title IN (SELECT title FROM job_listings);
```

Q：为什么我不能只用联接呢？   
A：也可以用联接，但有些人觉得子查询设计起来比较简单。在语法上能有其他选择是件好事。
相同的查询结果可以有不同的实现方式。

```
SELECT first_name, last_name, mc.phone
FROM my_contacts
WHERE zip_code = (SELECT zip_code FROM zip_code WHERE city = 'Memphis' AND state = 'TN');

SELECT first_name, last_name, mc.phone
FROM my_contacts mc
         NATURAL JOIN zip_code zc
WHERE zc.city = 'Memphis'
  AND zc.state = 'TN';
```

既然只是临时性的别名，就表示不会影响原始表或列的名称。

### 子查询的构造流程

子查询的棘手部分并非结构，而是找出需要作为子查询的查询、甚至是是否需要子查询。


| table my_contacts Field | table job_current Field | table job_desired Field | table job_listings Field |
| :--- |:--- |:--- |:--- |
| last\_name |contact_id |contact_id |job_id |
| first\_name | title |title |title |
| email | salary |salary_low |salary|
| gender | start_date | salary_high| zip |
| ...... | | available | description |
| prof\_id | | years_exp|
| zip_code |
| status_id |


```
create table job_current
(
    contact_id int           not null,
    title      varchar(16)   null,
    salary     DECIMAL(8, 2) null,
    start_date DATE          null,
    constraint job_current_pk
        primary key (contact_id)
);
```

**在所有联络人里，谁赚的钱最多？**

1. 分解问题
2. 找出能够回答部分问题的查询（既然我们创建的是非关联字查询（noncirrelated subquery），我们可以挑出部分问题并建立回答该部分的查询。）

```
SELECT MAX(salary) FROM job_current;
```
3. 继续分解

```
SELECT mc.first_name, mc.last_name FROM my_contacts AS mc;
```
4. 最后，找出串起两个查询的方式（我们需要知道my_contacts中的人名，还需要知道他们的薪水，才能比较出最高的薪资）
我们需要一个自然内联接来找出每个人的薪资信息：

```
SELECT mc.first_name, mc.last_name, jc.salary
FROM my_contacts AS mc
         NATURAL JOIN job_current AS jc;
```
5. 接下来加上WHERE子句以连接两段查询。

```
SELECT mc.first_name, mc.last_name, jc.salary
FROM my_contacts AS mc
         NATURAL JOIN job_current AS jc
WHERE jc.salary = (SELECT MAX(salary) FROM job_current);

# 或
SELECT mc.first_name, mc.last_name, jc.salary
FROM my_contacts AS mc
         NATURAL JOIN job_current AS jc
WHERE jc.contact_id = (SELECT jc.contact_id FROM job_current jc ORDER BY jc.salary DESC LIMIT 1);
```


### 子查询作为 SELECT 语句中选取的列之一

**如果子查询放在 SELECT 语句中，用于表示某个欲选取的列，则一次只能从一列返回一个值**。

```
create table zip_code
(
    zip_code varchar(16) null,
    city     varchar(16) null,
    state    varchar(16) null
);
```

```
SELECT mc.first_name, mc.last_name, (SELECT state FROM zip_code WHERE mc.zip_code = zip_code) AS state
FROM my_contacts mc;
```


### 范例：子查询搭配自然联接

Andy最近在吹嘘他的待遇有多么好，找出薪资比Andy高的人。

```
SELECT mc.first_name, mc.last_name, jc.salary
FROM my_contacts mc
         NATURAL JOIN job_current jc
WHERE jc.salary > (SELECT salary FROM job_current WHERE email = 'andy@weatherorama.com');
```

### 非关联子查询

**如果子查询可以独立运行且不会引用外层查询的任何结果，即称为非关联子查询**。

```
SELECT mc.first_name, mc.last_name, jc.salary
FROM my_contacts mc
         NATURAL JOIN job_current jc
WHERE jc.salary > (SELECT salary FROM job_current WHERE email = 'andy@weatherorama.com');
```

**非关联子查询**。软件先处理内层查询，查询结果再用于外层查询的 WHERE 子句（外层查询较晚处理）。但是内层查询完全不需要依赖
外层查询的值，它本身就是一个可以完全独立运行的查询。

### SQL 真情指数

**交叉联接是件非常浪费时间的事；关联子查询也会拖慢速度；联接比子查询更有效率。**

### 有多个值的非关联子查询：IN、NOT IN

```
SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc
         NATURAL JOIN my_contacts AS mc
WHERE jc.title IN (SELECT title FROM job_listings);

SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc
         NATURAL JOIN my_contacts AS mc
WHERE jc.title NOT IN (SELECT title FROM job_listings);
```

**非关联子查询使用 IN 或 NOT IN 来检查子查询返回的值是否为集合的成员之一。**

### 子查询练习

| my_contacts Field | profession Field | zip_code Field | status Field |
| :--- |:--- |:--- |:--- |
| contact_id |prof_id |zip_code |status_id |
| last_name | profession |city |status |
| first_name ||state ||
| email |
| gender |
| ...... |
| prof_id |
| zip_code |
| status_id |

| job_current Field | job_desired Field | job_listings Field |
|:--- |:--- |:--- |
|contact_id |contact_id |job_id |
| title |title |title |
| salary |salary_low |salary|
| start_date | salary_high| zip |
| | available | description |
| | years_exp|


| contact_interest Field | interests Field | contact_seeking Field | seeeking Field |
| :--- |:--- |:--- |:--- |
| contactact_id |interest_id |contactact_id |seeking_id|
| interest_id | interest |seeking_id |seeking|

1. 列出薪资等于 job_listings 表中的最高薪资的职务名称

```
SELECT title
FROM job_listings
WHERE salary = (SELECT MAX(salary) FROM job_listings); 
```

2. 列出薪资高于平均薪资者的姓名

```
SELECT mc.first_name, mc.last_name
FROM my_contacts mc
         NATURAL JOIN job_current jc
WHERE jc.salary > (SELECT AVG(salary) FROM job_current);
```

3. 请寻找网站设计员，但只列出其邮政编码与任何一个网站设计职缺的邮政编码相同的设计员

```
SELECT mc.first_name, mc.last_name, mc.phone
FROM my_contacts mc
         NATURAL JOIN job_current jc
WHERE jc.title = 'web design'
  AND mc.zip_code
    IN (SELECT zip FROM job_listings WHERE title = 'web design');

# 错误示例，只找到州，并未对应到设计人员
SELECT mc.first_name, mc.last_name
FROM my_contacts mc
         NATURAL JOIN zip_code zc
WHERE zc IN (SELECT zip FROM job_listings WHERE title = 'Web Designer');
```

4. 列出每个邮政编码涵盖的地区中当前薪资最高的人

```
# 自己答案（待验证）
SELECT c.zip_code, MAX(c.salary) max_salary
FROM (
         SELECT mc.first_name, mc.last_name, mc.zip_code, jc.salary
         FROM my_contacts mc
                  NATURAL JOIN job_current jc) c
GROUP BY zip_code;

# 教材答案（待验证）
SELECT last_name, first_name
FROM my_contacts
WHERE zip_code IN (SELECT mc.zip_code
                   FROM my_contacts mc
                            NATURAL JOIN job_current jc
                   WHERE jc.salary = (SELECT MAX(salary) FROM job_current));
```

### 关联子查询

**关联子查询**是指内层查询的解析需要依赖外层查询的结果。

示例（计算my_contacts 里的每个人各有几项兴趣，然后返回具有三项兴趣的人）：

```
SELECT mc.first_name, mc.last_name
FROM my_contacts mc
WHERE 3 = (SELECT COUNT(*) FROM contact_intest WHERE contact_id = mc.contact_id);
```

子查询依赖外层查询，它需要来自外层查询的结果，contacts_id的值，然后才能解析内层查询。
查询中使用的**别名（关联名称）** mc，是在外层查询中创建的。

### 一个搭配 NOT EXISTS 的（好用）关联子查询

关联子查询的常见用法，是找出所有外层查询结果里不存在于关联表里的记录。

找出my_contacts 里目前不在 job_current 表中的每个人。

```
SELECT mc.first_name, mc.last_name, mc.email
FROM my_contacts mc
WHERE NOT EXISTS(
    SELECT * FROM job_current jc WHERE mc.contact_id = jc.contact_id
    );
```

### EXISITS 与 NOT EXISITS

就像 IN 与 NOT IN，子查询也能搭配 EXISTS 与 NOT EXISTS 一起使用。

查询 my_contacts 表的数据，其中 contacts_id 曾出现在 contact_interest表。

```
SELECT mc.first_name, mc.last_name, mc.email
FROM my_contacts mc
WHERE EXISTS(
              SELECT * FROM contact_interest ci WHERE mc.contact_id = ci.contact_id
          );
```

练习：

查询每个人的电子邮件地址，电子邮件的主人至少拥有一项兴趣，但其在 job_current表中没有相应记录。

```
SELECT mc.first_name, mc.last_name, mc.email
FROM my_contacts mc
WHERE EXISTS(
        SELECT * FROM contact_interest ci WHERE mc.contact_id = ci.contact_id
    )

  AND NOT EXISTS(
        SELECT * FROM job_current jc WHERE mc.contact_id = jc.contact_id
    );
```

### SQL 工具包

- **Noncorrelated Subquery**
  - 非关联子查询。一个独立而且不引用outter query的任何部分。
- **Correlated Subquery**
  - 关联子查询。一个依赖outter query的返回结果的subquery。
- **Outer query**
  - 外层查询。包含 inner query/subquery的查询。
- **Inner query**
  - 内层查询。在查询内的查询，也称为subquery。
- **Subquery**
  - 子查询。被另一个查询包围的查询，也称为 inner query。





---
<h1 id="10">10 | 外联接、自联接与联合：新策略</h1>

---

**外联接**，它可以在表中没有匹配记录的情况下返回记录；**自联接**，它可以联接表本身，**联合**，它可以合并查询结果。

外连接返回某张表的所有行，并带有来自另一张表的条件相符的行。使用内联接（equijoin）时，虽然要**比对来自两张表的行，
但表的顺序并无影响。**

### 一切都跟左、右有关

外联接更加注重**两张表之间的关系**

**LEFT OUTER JOIN（左外联接）会匹配左表中的每一行及右表中符合条件的行**。当左表与右表具有一对多关系时，
左外联接特别有用。

在 LEFT OUTER JOIN 中，出现在 FROM 后、联接前的表称为**左表**，而出现在联接后的表当然就是**右表**。

girls表

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| girl\_id | int\(11\) | YES |  | NULL |  |
| girl | varchar\(20\) | YES |  | NULL |  |
| toy\_id | int\(11\) | YES |  | NULL |  |

toys表

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| toy\_id | int\(11\) | YES |  | NULL |  |
| toy | varchar\(20\) | YES |  | NULL |  |


equijoin的运作方式

```
SELECT g.girl, t.toy
FROM girls g
         INNER JOIN toys t ON g.toy_id = t.toy_id;
```

每个女孩拥有的玩具

```
SELECT g.girl, t.toy
FROM girls g
         LEFT OUTER JOIN toys t ON g.toy_id = t.toy_id;
```
查询结果：

| girl | toy |
| :--- | :--- |
| Cindy | hula hoop |
| Jane | toy soldiers |
| Sally | harmonica |
| Mandy | NULL |

外联接与内联接的差别：**外联接一定会提供数据行，无论该行能否在另一个表中找出相匹配的行。**   

**左外联接的结果集中的 NULL 表示右表中没有找到与左表相符的记录。**

出现 NULL 是告诉我们没有相匹配的行。例如女孩玩具的查询结果集中的 NULL 表示没人拥有该玩具。

```
SELECT g.girl, t.toy
FROM toys t
         LEFT OUTER JOIN girls g ON g.toy_id = t.toy_id;
```

查询结果：

| girl | toy |
| :--- | :--- |
| Jane | toy soldiers |
| Sally | harmonica |
| Cindy | hula hoop |
| NULL | balsa glider |
| NULL | baseball cards |
| NULL | tinker toys |
| NULL | etch-a-sketch |
| NULL | slinky |

查询结果中，列的顺序就是 SELECT 指定的顺序。左联接对于结果行的排列顺序没有影响。


### 外联接与多个相符结果

虽然在另一个表中没有相符的记录，但你还是会取得数据行，在匹配出多条记录时就会取出多行。


### 右外联接

右外联接与左外联接完全一样，除了它是用右表与左表比对。

**右外联接会根据左表评估右表。**

以下两个查询都以girls为左表。且查询结果相同。

```
SELECT g.girl, t.toy
FROM girls g # 左表
         LEFT OUTER JOIN toys t ON g.toy_id = t.toy_id;

SELECT g.girl, t.toy
FROM toys t # 右表
         RIGHT OUTER JOIN girls g ON g.toy_id = t.toy_id;
```

查询结果。

| girl | toy |
| :--- | :--- |
| Cindy | hula hoop |
| Jane | toy soldiers |
| Sally | harmonica |
| Mandy | NULL |

Q：如果有左外联接与右外联接，有可以返回两种联接结果的联接吗？   
A：有些 RDBMS 系统可以做到，称之为全外联接（FULLOUTERJOIN）。不可以做到的系统包括MySQL、SQL Server、Access。


### 自引用外键

“自引用”表示它是引用同一张表内另一列的键。

**自引用外键是出于其他目的而用于同一张表的主键**。

```
ALTER TABLE clown_info
    ADD id INT PRIMARY KEY AUTO_INCREMENT FIRST;
	
alter table clown_info add boss_id int not null;
```
clown_info表

| Field | Type | Null | Key | Default | Extra |
| :--- | :--- | :--- | :--- | :--- | :--- |
| id | int\(11\) | NO | PRI | NULL | auto\_increment |
| name | varchar\(50\) | YES |  | NULL |  |
| last\_seen | varchar\(50\) | YES |  | NULL |  |
| appearance | varchar\(50\) | YES |  | NULL |  |
| activities | varchar\(50\) | YES |  | NULL |  |
| boss\_id | int\(11\) | NO |  | NULL |  |

```
SELECT id, name, boss_id FROM clown_info;
```

| id | name | boss\_id |
| :--- | :--- | :--- |
| 1 | Elsie | 3 |
| 2 | Pickles | 5 |
| 3 | Snuggles | 10 |
| 4 | Mr. Hobo | 3 |
| 5 | Clarabelle | 10 |
| 6 | Scooter | 3 |
| 7 | Zippo | 3 |
| 8 | Babe | 5 |
| 9 | Bonzo | 5 |
| 10 | Mister Sniffles | 10 |


新设立的boss_id列，我们直接把它加入clown_info 表。这部分存储了自引用外键。
引用同一张表中的id字段，所以能找出谁是Elsie的头领。Mister Sniffles 的boss_id就是他自己的id。

### 联接表与它自己

利用 SELECT 可以轻易地列出每个小丑的姓名及他们头领的ID。

```
SELECT name, boss_id FROM clown_info;
```

其实我们更需要小丑的姓名与他们的头领的姓名。

假设有两个完全相同的表 clown_info1 与 clown_info2

```
SELECT c1.name, c2.name
FROM clown_info1 c1
         INNER JOIN clown_info2 C2 ON c1.boss_id = c2.id;
```

### 我们需要自联接

在规范的数据库中，相同的表不会出现两次。于是，可以**改用自联接（self-join）以模拟联接两张表的效果**。

```
SELECT c1.name, c2.name
FROM clown_info c1
         INNER JOIN clown_info C2 ON c1.boss_id = c2.id;
```

不需要两张相同的表，而是使用 clown_info 两次。

**自联接能把单一表当成两张具有完全相同的信息的表来进行查询**。


### 另一种取得多张表内容的方式 - - 利用 UNION （联合）

UNION 根据我们在 SELECT 中指定的列，把两张或更多张表的查询结果合并至一个表中，**结果集中没重复记录**。

```
SELECT title FROM job_current
UNION
SELECT title FROM job_desired
UNION
SELECT title FROM job_listings;
```

### UNION 的使用限制

在每个 SELECT 语句中加入 ORDER BY 子句，使得“职务”依序排序。

```
SELECT title FROM job_current ORDER BY title
UNION
SELECT title FROM job_desired ORDER BY title
UNION
SELECT title FROM job_listings ORDER BY title;
```

上面查询无法使用，得到一个错误信息，因为数据库软件不知道如何解释多个 ORDER BY 。

UNIION 只能接受一个  ORDER BY 且必须位于语句末端。这是因为 UNION 已经把多个 SELECT 语句
的查询结果串联起来并分组了。

```
SELECT title FROM job_current
UNION
SELECT title FROM job_desired
UNION
SELECT title FROM job_listings
ORDER BY title;
```

### SQL UNION（联合）规则的运作

* 每个 SELECT 语句中列的数量必须一致。不可以由第一条语句选取了两列，由其他语句却只选取一列。
* 每个 SELECT 语句包含的表达式与统计函数也必须相同。
* SELECT 语句的顺序不重要，不会改变结果。
* SQL默认会清除联合的结果中的重复值。
* 列的数据类型必须相同或者可以互相转换（以联合 INTERGER 与 VARCHAR类型为例，因为 VARCHAR 无法转换成整型，
所以查询结果会把 INTERGER 转换为 VARCHAR）。
* 如果出于某些原因而需要看到重复数据，可以使用 UNION ALL 运算符。这个运算符返回每个相符的记
录，而不只是没有重复记录。

### UNIOON ALL

UNIOON ALL 的运作方式与 UNION 相同，只不过它会返回列的所有内容，而不是每个的复制实例。

```
SELECT title FROM job_current
UNION ALL
SELECT title FROM job_desired
UNION ALL
SELECT title FROM job_listings
ORDER BY title;
```

### 从联合创建表

```
CREATE TABLE my_union AS
SELECT title FROM job_current
UNION
SELECT title FROM job_desired
UNION
SELECT title FROM job_listings;
```

### INTERSECT（交集）与 EXCEPT（差集）

**不幸的是，MySQL不支持INTERSECT、EXCEPT操作符**。

```
SELECT title FROM job_current
INTERSECT
SELECT title FROM job_desired;

SELECT title FROM job_current
EXCEPT
SELECT title FROM job_desired;
```

INTERSECT运算符用于两个查询，应用以下规则：   
* 列的顺序和数量必须相同。
* 相应列的数据类型必须兼容或可转换。

EXCEPT 返回只出现在第一个查询，而不在第二个查询中的列。


### [MySQL INTERSECT模拟](https://www.yiibai.com/mysql/sql-union-mysql.html)

// TODO

### 把子查询转换为联接

几乎所有能用子查询办到的事都能用联接实现。

```
# 子查询
SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc
         NATURAL JOIN my_contacts AS mc
WHERE jc.title IN (SELECT title FROM job_listings);

# 以内联接的方式重写 WHERE 子句
SELECT mc.first_name, mc.last_name, mc.phone, jc.title
FROM job_current AS jc NATURAL JOIN my_contacts AS mc
INNER JOIN job_listings j1
ON jc.title = j1.title;
```

INNER JOIN 只会呈现 jc.title = j1.title 的记录。相当于加上子查询的 WHERE 子句：WHERE jc.title IN (SELECT title FROM job_listings);
因此取得相同的结果。

列出薪资等于 job_listings 表中最高薪资的职务名称。

```
SELECT title
FROM job_listings
WHERE salary = (SELECT MAX(salary) FROM job_listings);

SELECT title FROM job_listings
ORDER BY salary DESC
LIMIT 1;
```

Q：最好使用子查询吗？   
A：不是。


列出薪资高于平均薪资者的姓名。

```
SELECT mc.first_name, mc.last_name
FROM my_contacts mc
         NATURAL JOIN job_current jc
WHERE jc.salary > (SELECT AVG(salary) FROM job_current);
```

Q：最好使用子查询吗？   
A：是的。

### 把自联接变成子查询

变身前

```
SELECT c1.name, c2.name
FROM clown_info c1
         INNER JOIN clown_info C2 ON c1.boss_id = c2.id;
```

变身后

```
SELECT c1.name,
       (SELECT name FROM clown_info WHERE c1.boss_id = id) AS boss
FROM clown_info c1;
```

自联接转变为子查询时，因为子查询需依赖外层的结果才能取得正确的 boss_id，所以
子查询将是关联子查询，而关键性可由 SELECT 选取的列揭示。

### SQL工具包

- SELF-REFERENCEING FOREIGN KEY
  - 自引用外键。这种外键就是同一张表的主键，但作为其他用途。
- LEFT OUTER JOIN
  - 左外联接。LEFT OUTER JOIN 接受左表中的所有记录。并从右表比对出相符记录。
- RIGHT OUTER JOIN
  - 右外联接。RIGHT OUTER JOIN 接受右表中的所有记录，并从左表比对出相符的记录。
- SELF-JOIN
  - 自联接。SELF-JOIN 能用一张表做出联接两张完全相同表的效果。
- UNION 与 UNION ALL 
  - UNION（联合）根据 SELECT 指定的列合并两个或多个查询的结果为一张表。
  - UNION 默认为隐藏重复的值；
  - UNION ALL 则可以包含重复的值。
- CREATE TABLES AS
  - 使用本命令从任何 SELECT 语句的结果创建表。
- INTERSECT
  - 使用这个关键字返回同时存在于第一个与第二个查询中的值。
- EXCEPTE
  - 使用这个关键字返回在第一个查询中但不在第二个查询中的值。


### 补充（联接 VS 子查询）

联接：在结果集需要取得来自多张表的列时，我任然是比较好的选择。事实上，我是唯一的选择。

子查询：所以你才不擅长聚合值/统计值。在一个没有子查询的 WHERE 子句中无法使用统计函数。统计运算弥补了我不能返
回多列的缺点。你实在太复杂了。








---
<h1 id="11">11 | 约束、视图与事务：人多手杂，数据库受不了</h1>

---

### 检查约束：加入 CHECK

**约束**（constraint）限定了可以插入列的内容，而在我们创建表时就要加入约束。比如文中稍早出现国的约束：
NOT NULL、PRIMARY KEY、FOREIGN KEY、UNIQUE。

**CHECK（检查）约束限定允许插入某个列的值（比如枚举值）。它与 WHERE 子句都使用相同的条件表达式。**

例如，硬币面额只可能是P（penny）、N（nickel）、D（dime）、Q（quarter），均以首字母缩写表示，创建小猪存
钱罐时，即可用 CHECK 约束来限定插入 coin 列的值。

```
CREATE TABLE piggy_bank
(
    id   INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    coin CHAR(1) CHECK ( coin IN ('P', 'N', 'D', 'Q') )
);
```

**如果插入的值无法通过 CHECK 条件，则出现错误信息。**

**注意：**   
**在MySQL里，无法以 CHECK 强化数据完整性。**   
**在MySQL中，虽然你可在创建表时加上检查约束，但它不会有什么帮助。MySQL 只会忽略它。**

### 为列设定检查约束

```
ALTER TABLE my_contacts
    ADD CONSTRAINT CHECK gender IN ('M', 'F');
```

```
CREATE TABLE mystery_table
(
    # 输入的值必须大于200
    column_1 INT(4) CHECK ( column_1 > 200 ),
    # 只要不是字符 x、y、z，都可以输入
    column_2 CHAR(1) CHECK ( column_2 NOT IN ('X', 'Y', 'Z')),
    # 字符串的第一个字符必须为A
    column_3 VARCHAR(3) CHECK ( 'A' = SUBSTRING(column_3, 1, 1)),
    # 字符串的第一个字符必须为A，第二个字符必须时9
    column_4 VARCHAR(3) CHECK ( 'A' = SUBSTRING(column_4, 1, 1) AND '9' = SUBSTRING(column_4, 2, 1))
);
```

Q：所以说，能用在 WHERE 子句中的东西都能用于 CHECK?   
A：差不多。所有条件运算符-- AND、OR、IN、NOT、BETWENN等都能用于 CHECK 条件，甚至还能和上例一样结合体哦阿健运算。
但是无法使用子查询。

Q：如果无法在MySQL里使用检查约束，该如何代替这个功能呢？   
A：真是个很难回答的问题。有些人改用触发机制（trigger）--在满足特定条件时才执行的查询。但是trigger不像 CHECK 这么
简单，而且超出本书的讨论范围。   

Q： 如果试着插入无法满足 CHECK 条件的值，会发生什么？   
A：你会看到错误信息而且不会插入任何记录。   

Q：这样做有什么好处吗？   
A：检查约束可能确保输入数据的合理性。你不会在最后发现一堆神秘的值。   

### 创建视图

创建视图非常简单，只需在查询中加入 CREATE  VIEW 语句。

```
CREATE VIEW web_designers AS
SELECT mc.first_name, mc.last_name, mc.phone, mc.email
FROM my_contacts mc
         NATURAL JOIN job_desired jd
WHERE jd.title = 'Web Designer';
```

### 查看视图

查看视图的内容，可以把它想成一张表。

```
SELECT * FROM web_designers;
```

### 视图的实际行动

当在查询中使用视图时，它的行为方式与子查询一样。

```
SELECT * FROM web_designers;
```

把其转换为子查询

```
SELECT *
FROM (
         SELECT mc.first_name, mc.last_name, mc.phone, mc.email
         FROM my_contacts mc
                  NATURAL JOIN job_desired jd
         WHERE jd.title = 'Web Designer') AS web_designers;
```
 
 我们给子查询一个别名，以便查询把它当成一般的表。

Q：为什么要有“AS web_designers”？为什么需要这个部分？   
A：**FROM子句需要表**。当 SELECT 语句的结果是一个虚拟表时，若没有别名，SQL就无法取得其中的表。

### 何为视图

基本上视图是一个只有在查询中使用 VIEW 时才存在的表。它被视为**虚拟表（virtual table）**，因为其
行为和表一样，也能执行表可用的操作。

但虚拟表不会一直保存在数据库里。

#### 视图对数据库的好处
1. 视图把复杂查询简化为一个命令，带来更轻松的生活；
2. 即使一直改变数据库结构，也不会破坏依赖表的应用程序；
3. 创建视图可以隐藏读者无需看到的信息。


### 利用视图进行插入、更新与删除

视图不仅能用于 SELECT，从表中选择信息，有时候，它还可以用于 UPDATE、INSERTT、DELETE数据。


Q：所以我可以创建一个视图，用于实际修改表？   
A：**的确可以，但不值得这么麻烦**。   

如果你的**视图使用统计函数（例如 SUN、COUNT、AVG）**，则无法用视图改变数据。如果你的视图包含
GROUP BY、DISTINCT、HAVING，我们也不可以改变任何数据。

在多数情况下，用传统方式做 INSERT、UPDATE、DELETE 更容易。

```
CREATE TABLE piggy_bank
(
    id        INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    coin      CHAR(1)            NOT NULL,
    coin_year CHAR(4)
);
```

```
INSERT INTO acc_adapter.piggy_bank (coin, coin_year)
VALUES ('Q', '1950'),
       ('P', '1972'),
       ('N', '2005'),
       ('Q', '1999'),
       ('Q', '1981'),
       ('D', '1940'),
       ('Q', '1980'),
       ('P', '2001'),
       ('D', '1926'),
       ('P', '1999');
```

查询结果

| id | coin | coin_year |
| :--- | :--- | :--- |
| 1 | Q | 1950 |
| 2 | P | 1972 |
| 3 | N | 2005 |
| 4 | Q | 1999 |
| 5 | Q | 1981 |
| 6 | D | 1940 |
| 7 | Q | 1980 |
| 8 | P | 2001 |
| 9 | D | 1926 |
| 10 | P | 1999 |


设计一个只会呈现 pb_quarters、pb_dimes 的视图。

```
CREATE VIEW pb_quarters AS
SELECT * FROM piggy_bank
WHERE coin = 'Q';

CREATE VIEW pb_dimes AS
SELECT * FROM piggy_bank
WHERE coin = 'D' WITH CHECK OPTION;
```

### 练习

以下操作将适当地运行。

```
INSERT INTO pb_quarters (coin, coin_year)
VALUES ('Q', '1993');
```

以下操作将插入新的值至原始表中，因为创建视图时带有 WHERE 子句，甚至可能有人意味它不会成功。

```
INSERT INTO pb_quarters (coin, coin_year)
VALUES ('D', '1942');
```

以下操作因为 CHECK OPTION 子句会产生错误信息。虽然它是把数据输入视图的查询，但在添加数据前
必须先通过 WHERE 子句的验证。

```
INSERT INTO acc_adapter.pb_dimes (coin, coin_year)
VALUES ('Q', '2005');

# [2021-07-09 09:01:44] [HY000][1369] CHECK OPTION failed 'acc_adapter.pb_dimes'
```

以下操作对表没有任何影响，因为它只能找到面额是 “Q”的硬币的记录。

```
DELETE FROM pb_quarters WHERE coin = 'N' OR coin = 'P' OR coin = 'D';
```

以下操作对表没有任何影响，因为在视图 pb_quarters 里没有 coin = 'P' 的值。

```
UPDATE pb_quarters SET coin = 'Q' WHERE coin = 'P';
```

| id | coin | coin_year |
| :--- | :--- | :--- |
| 1 | Q | 1950 |
| 2 | P | 1972 |
| 3 | N | 2005 |
| 4 | Q | 1999 |
| 5 | Q | 1981 |
| 6 | D | 1940 |
| 7 | Q | 1980 |
| 8 | P | 2001 |
| 9 | D | 1926 |
| 10 | P | 1999 |
| 11 | Q | 1993 |
| 12 | D | 1942 |

```
UPDATE pb_quarters SET coin_year = '2021' WHERE coin = 'Q';
```

更新视图后，原始表的数据将被修改。

| id | coin | coin_year |
| :--- | :--- | :--- |
| 1 | Q | 2021 |
| 4 | Q | 2021 |
| 5 | Q | 2021 |
| 7 | Q | 2021 |
| 11 | Q | 2021 |


```
UPDATE pb_quarters SET coin_year = '2222';
```

| id | coin | coin_year |
| :--- | :--- | :--- |
| 1 | Q | 2222 |
| 4 | Q | 2222 |
| 5 | Q | 2222 |
| 7 | Q | 2222 |
| 11 | Q | 2222 |


### 带有 CHECK OPTION 的视图

CHECK OPTION 检查每个进行 INSERT 或DELETE 的查询，它根据视图中的 WHERE 子句来判断这些查询是否可执行。

Q： 如果使用MySQL，可以利用具有 CHECK OPTION 的视图创建类似于 CHECK CONSTRAINT 的机制吗？   
A：**可以，你的视图能够精确地反映表的内容，又能强迫 INSERT 语句服从 WHERE 子句的条件**。   

**使用 MySQL 时，可用 CHECK OPTION 模仿 CHECK CONSTRAINT 的功能**。

比如，我们可以创建一个 my_contacts 表的视图，通过视图更新数据，每次尝试更新时，若不满足 WHERE 条件，
就会出现错误信息。


### 练习

如何为 my_contacts 创建一个视图，用于限制只能在性别字段里填入 “M”或“F”呢？


### 可更新视图

**可更新视图（updatable view）**就是可以改变底层表的视图。

**可更新视图包括引用表里所有为 NOT NULL 的列**。

使用场景：
1. 如果视图只有一列且底层表的其他列都可以指定为 NULL 或默认值，用视图来 INSERT 才会合理；
2. 可为视图加上 WHERE 子句以约束 INSERT 的内容，在 MySQL 中用 CHECK OPTION 模仿 CHECK CONSTRAINT 的功能；
3. 也可以只更新视图，此时的 UPDATE 查询不可包含统计类型的运算符，如 SUM、COUNT、AVG，像 BETWWEN、HAVING、IN、
NOT IN 这类运算符均不可使用。

### DROP VIEW

 不需要的视图，请利用 DROP VIEW 语句清理空间。
 
```
DROP VIEW pb_quarters;
DROP VIEW pb_dimes;
```


Q：可以查看自己创建的视图吗？   
A：可以利用下面两个命令
```
SHOW TABLES

# 或

DESC

```

Q：如果我卸除了有视图的表，会发生什么事？   
A：看情况而定。有些RDBMS允许使用视图，但不会返回任何数据。MySQL会阻止我们卸除视图，
除非它有底层表，但我们却可以卸除与视图有关的表。有些RDBMS另有不同的反应。可自行实验。
一般而言，最好先卸除视图，然后再卸除它依据的表。

Q：多人操作数据库时，显然 CHECK CONSTRAINT与视图很有帮助。但如果两个人同时改变同一
个列，有会发生什么呢？   
A：关于这个问题，我们应该从事务（transaction）开始讲起。    

**当数据库的使用者不只一人时，CHECK CONSTRAINT 与视图均有助于维护控制权。**


### 事务

如果一系列SQL语句能以组的方式一起执行，而且发生意外时SQL语句还能回到尚未执行前的状态，
那该有多好啊？

**事务**（transaction）是一群可完成一组工作的SQL语句。

```
这三项操作形成一组工作，也就是一个事务 

|    +-----------------------+
|----| 如果账户A余额 >= 1000  |
|    +-----------------------+
|
|    +--------------------+
|----| 从支票账户减去1000  |
|    +--------------------+
|
|    +--------------------+
|----| 为存款账户加上1000  |
|    +--------------------+
```


```
同时试着执行相同事务（不同的取款机执行取款操作）

|    +---------------------+                  |    +---------------------+
|----| 如果账户余额 >= 300  |                  |----| 如果账户余额 >= 300 |
|    +---------------------+                  |    +---------------------+
|                                             |
|    +----------------+                       |    +-----------------+
|----| 从账户减里去300 |                       |----| 从账户减里去300 |
|    +----------------+                       |    +-----------------+
|                                             |
|    +--------------+                         |    +----------------+
|----| 奉上300元现金 |                         |----| 奉上300元现金  |
     +--------------+                              +----------------+
                       >                    <
                        >                  <
                         >                <
                          +--------------+
                          |  账户余额350  |
                          +--------------+
```





**在事务过程中，如果所有步骤无法不受干扰地完成，则不该完成任何单一步骤**


### 经典 ACID 检测

ACID 帮助你判断SQL步骤是否为一个事务。

- **ACID: ATOMICITY**
  - 原子性。事务里的每一个步骤都必须完成，否则只能都不完成。不能只执行部分事务。
- **ACID: CONSISTENCY**
  - 一致性。事务完成后应该维持数据库的一致性。
- **ACID: ISOLATION**
  - 隔离性。表示每次事务都会看到具有一致性的数据库，无论其他事务由什么行动。
- **ACID: DURABILITY**
  - 持久性。事务完成后，数据库需要正确地存储数据并保护数据免受断电或其他威胁的伤害。
通常把事务记录存储在主数据库以外的地方。


### SQL 帮助你管理事务

- **START TRANSACTION**
  - START TRANSACTION 持续追踪后续所有 SQL 语句，直到你输入 COMMIT 或 ROLLBACK。
- **COMMIT**
  - 提交所有程序代码造成的改变。在你 COMMIT 之前数据库都不会发生任何改变。
- **ROLLBACK**
  - 回滚，回到事务开始前的状态。


```
START TRANSACTION;
UPDATE piggy_bank SET coin = 'Q' WHERE coin = 'P'
  AND coin_year < 1970;
COMMIT;
```

```
START TRANSACTION;
UPDATE piggy_bank SET coin = 'Q' WHERE coin = 'P'
  AND coin_year < 1970;
ROLLBACK ;
```




### 如何让事务在MySQL下运作

在MySQL下使用事务前，需要先采用正确的**存储引擎（storage engine）**。

```
SHOW CREATE TABLE acc_adapter.ht_offline_repay;


CREATE TABLE `ht_offline_repay` (
  `id` bigint(20) NOT NULL,
  `project_no` varchar(32) COLLATE utf8_bin DEFAULT NULL COMMENT '项目编号',
  `batch_no` varchar(32) COLLATE utf8_bin DEFAULT NULL COMMENT '批次号',
  `total_amt` decimal(12,2) DEFAULT NULL COMMENT '批次交易总金额',
  `batch_date` date NOT NULL COMMENT '跑批日期',
  `created_date` datetime(6) DEFAULT NULL,
  `last_modified_date` datetime(6) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='汇通-线下还款原始数据'
```

结束括号后的文字，它说明了数据如何存储，以及使用的字符集。目前使用默认值就够了。

两种支持事务的存储引擎：BDB、InnoDB 。

改变存储引擎的语法：

```
ALTER TABLE your_table TYPE = InnoDB;
```

Q：事务一定要以 START TRANSACTION 开始吗？COMMIT 与 ROLLBACK 可以独立运作吗？   
A：必须使用 START TRANSACTION 告诉 RDBMS “事务开始”，才能追踪事务开始的地方并知道恢复的程度。

Q：可以使用  START TRANSACTION 来测试查询吗？   
A：可以，而且也应该如簇，利用这个方法，可以在数据库里实现影响数据的查询，又不会在做错事时无法挽救
对表做的改变。但记得在改变完成后输入 COMMIT 或 ROLLBACK。

Q：为什么需要 COMMIT 与 ROLLBACK?   
A：RDBMS 会记录事务过程中的每个操作，称为事务日志（trasaction log），操作越多，日志越大。事务最好
留待正真需要恢复原功能时使用，以避免存储空间的浪费，也避免了 RDBMS 花费太多精力来追踪我们的每个操作。


### SQL工具包

- VIEWS
  - 视图。使用视图把查询结果当成表。很适合简化复杂查询。
- UPDATABLE VIEWS
  - 可更新表。有些视图能用以改变它底层的实际表。这类视图必须包含底层表的所有 NOT NULL 列。
- NON-UPDATABLE VIEWS
  - 无法对表底层执行 INSERT 或 UPDATE 操作的视图。
- CHECK CONSTRAINTS
  - 检查约束。可以只让指定值插入或更新至表里。
- CHECK OPTION
  - 创建可更新视图时，使用这个关键字强迫所有插入与更新的数据需满足视图里的 WHERE 条件。
- TRANSACTIONS
  - 事务。一组必须同进退的查询。如果这些查询无法不受干扰地全部执行完毕，则不承认其中的部分查询造成的改变。
- START TRANSACTION
  - 这条语句告诉 RDBMS 开始事务。在 COMMIT执行前改变都不具永久性。除非出现 COMMIT 或 ROLLBACK，否则都处于事务过程中。




---
<h1 id="12">12 | 安全性：保护你的资产</h1>

---

SQL 允许我们控制对某个数据库可做与不可做得事项。但在这么做前，需要先给每个数据库用户一个**用户账号（user account）**。


### 保护用户账号：root

默认情况下，第一位用户--**根**用户（root）具有所有数据库操控能力。这点很重要，因为根用户必须可为其他用户
创建账号。 

MySQL设定根用户密码得方式很简单：

```
SET PASSWORD FOR 'root'@'localhost' = PASSWORD ('B4DC10WNz');
```

* 根用户名称就是“root”
* “localhost”代表安装与运行SQL软件得机器
* 'B4DC10WNz'这部分是我们为根用户选择的密码

其他 RDBMS 使用的方式则各有不同。例如 Oracle 采用：

```
alter user identified by new-password;
```

如果使用图形假面操作数据库，或许有更为简单的对话框形式来改变密码。**重点在于一定要设置密码**。


### 添加新用户

Q：**SQL 如何存储用户的信息**？   
A：当然是存储在表中！SQL 也以数据库存储它本身的数据，包括用户id、用户名称与密码以及各个用户对特定数据库的操作权限。

**SQL并未指定如何管理用户**。请参考不同RDBMS的说明文档。

添加新用户(用户名：elsie，密码：cl3v3rp4s5w0rd)

```
CREATE USER elsie IDENTIFIED BY 'cl3v3rp4s5w0rd';
```


Q：创建账号的同时，可以直接把Elsle的操作范围限定在特定表上吗？   
A：可以，但有时在创建新账户时，我们并不知道需要授予何种权限。（一般先创建用户，然后再授予用户需要的特殊权限）


### GRANT ... ON ... TO 语句为用户授予操作数据库的特权

**使用 GRANT 语句可以控制用户对表和列可执行的操作**。

**就算在表中，也可能需要权限：部分用户可看到特定列，但其他人不行**。


把指定表（clown_info）的某项权限（SELECT）授予指定的用户（elsie）

```
GRANT SELECT ON clown_info TO elsie
```

每次授予表的权限都需要另写一段 GRANT 语句。

```
# 1. 授予Doc插入（INSERT）内容至magic_animals表的权限

GRANT INSERT ON magic_animals TO doc;

# 2. 授予Happy与Sleepy删除（DELETE）chores表内容的权限

GRANT DELETE ON chores TO happy, sleepy;

# 3. 授予Happy与Sleepy删除（DELETE）chores表内容的权限并可把这个权限授予其他人

GRANT DELETE ON chores TO happy, sleepy WITH GRANT OPTION;

# 4. 让Dopey只能从chores表中选择（SELECT）chore_name列

GRANT SELECT (chore_name) ON chores TO dopey;

# 5. 授予Sneezy选择（SELECT）与插入（INSERT）内容至talking_animals表的权限

GRANT SELECT, INSERT ON talking_animals TO sneezy;

# 6. 授予Bashful选择（SELECT）、更新（UPDATE）、插入（INSERT）、删除（DELETE）talking_animals表内容的权限

GRANT ALL ON talking_animals TO bashful;

# 7. 把操作表chores的所有权限授予所有用户

GRANT ALL ON chores TO bashful, doc, dopey, happy, sleepy, sneezy;

# 8. 为Doc设定 SELECT权限，权限运用范围是 woodland_cottage 数据库中的所有表

GRANT SELECT ON woodland_cottage.* TO doc;

```

### GRANT 的各种变化

**1、可用同一个 GRANT 语句为多位用户设定权限**   
  每个提到名称的用户都会被授予相同权限   
  
**2、WITH GRANT OPTION 让用户能把刚刚获得的权限授予其他用户**   
如果某人获得了 SELECT 表 chores的权限，他可以把这个权限授予其他人   

**3、指定用户可于某个表中使用的列，而不是允许用户操作整张表**   
SELECT 权限也仅能限于单一列。用户看到的输出将出自指定的列   

**4、一段语句可对表指定超过一种权限**   
列出所有要授予用户的表操作权限并以逗号分隔每种权限   

**5、GRANT ALL 把 SELECT、UPDATE、INSERT、DELETE 指定表内容得权限都授予用户了**   
这只是“允许用户对某张表执行 SELECT、UPDATE、INSERT、DELETE 操作”的缩写方式   

**6、使用  ```database_name.* ```可把权限范围运用到数据库中的每张表上**   
与 SELECT 语句的通配符（*）相似，代表数据库中的所有表   


### 撤销权限：REVOKE

REVOKE 语句的语法几乎与 GRANT 完全相同。只是把 “GRANT” 换成 “REVOKE”，把 “TO” 换成 “FROM” 而已。

把授予Elise的 SELECT 权限收回。

```
REVOKE SELECT ON clown_info FROM elise;
```

也可以只撤销 WITH GRANT OPTION，**但不触及权限**。

例中，happy 与 sleepy 还能对 chores 表执行 DELETE，但不能在把删除操作的权限授予其他人。

```
REVOKE GRANT OPTION ON
DELETE ON chores 
FROM happy , sleepy;
```


### 撤销授权许可（GRANT OPTION）

root用户给了 sleepy 对 chores 表执行 DELETE 操作的权限并附有 GRANT OPTION，然后 sleepy
又给 sneezy 对 chores 表的 DELETE 权限。

如果root用户改变心意，撤销了sleepy 的权限，则 sneezy 的该项权限也会被撤销，即使根用户只是
对 sleepy 撤销了权限。

REVOKE 语句的副作用就是让 sneezy 也一起失去了权限。有两个关键字可用于控制撤销的范围。

RESTRICT 与 CASCADE 。


### 具精确度的撤销操作

使用关键字 CASCADE 与 RESTRICT 来更精确的锁定目标用户，决定谁会失去特权，谁能保持特权，
可以确保不影响目标以外的用户。

**CASCADE** 表示权限的撤销具有连锁反应，包括目标在内的被授权人的权限都会被撤销。

```
REVOKE DELETE ON chores FROM sleepy CASCADE;
```

**RESTRICT** ，若是被撤销权限的目标用户已把权限授予他人，在 REVOKE 语句中加入 RESTRICT
 可返回错误信息（表示有人会受影响，两方的权限都会被保留）。


撤销某些个权限时，可以指定需要撤销的权限，也可以全部撤销后重新赋值。


习题：有人一直把错误的权限授予 Elsie，请写下核实的 REVOKE 语句，让 Elsie 回到安全的只有 
SELECT 权限的状态。

```
GRANT INSERT(location), DELETE ON locations TO elsie;

#看起来这里可能要用GRANT来确定Elsie还是能对locations表进行SELECT操作。
#而且，我们最好确认她不会把同样的权限授予其他人。

REVOKE GRANT INSERT(location), DELETE ON locations FROM elsie;
```

Q：指定列名的 GRANT 语句还是让我念念不忘。如果只对表中的某一列授予 INSERT 权限会发生什么事？   
A：这种情况的INSERT实际上是个无用的权限。如果只能插入某列的值，就无法真正地插入一条新记录到
表中。除非在那张表中只有GRANT指定的列必须有内容。

Q：还有其他无用的权限吗？   
A：几乎所有针对列的权限都没有用，除非是GRANT语句中与SELECT有关的权限。

Q：假设我想添加一个用户，让他可以选取我所有的数据库中的所有表，有比较简单的设定方法吗？   
A：这个问题要看每个人使用的RDBMS而定，本章的很多问题也一样。在MySQL中，可以授予全局权限：
```
# 第一个星号代表所有的数据库，第二个星号表示所有表。
GRANT SELECT ON *.* TO elsie;
```

Q：如果未指定 REVOKE 的使用方式，CASCADE是默认值吗？   
A：CASCADE通常是默认值，不过请你先参考 RDBMS 的说明文档。

Q：如果我对用户根本没有的权限下了 REVOKE 命令，会发生什么事？   
A：会出现错误信息，告诉你一开始就根本没有 GRANT !

Q：在前面的例子中，根用户撤销了 sneezy 的某个权限，如果有两名其他用户授予同样权限，会发生什么事？   
A：有些系统在 GRANT 使用 CASCADE 时不会注意谁发出了 GRANT语句，有些系统则会忽略。具体需要参考
RDBMS 说明文档。

Q：除了表和列以外，还有什么可以使用 GRANT 与 REVOKE ？   
A：这两个语句也能用于视图，但不包括不可更新的视图，此时，就算具有 INSERT 权限，也一样无法插入新
数据。因为视图和表几乎一样，所以能单独授予视图中的某列操作权限。


### 使用角色

我们需要授予一群人所需权限，同时又让他们每个人都有自己的账号的方式。

此时需要**角色**（role）。角色是把特定权限汇集成组，再把组权限授予一群人的方式。角色成为一个数据
库对象，用于数据库变动时依需求调整，而不逐一指定、调整每名用户的权限。



P543

