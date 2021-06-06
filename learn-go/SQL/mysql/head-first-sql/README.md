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



















427
















---
<h1 id="10">10 | 外联接、自联接与联合：新策略</h1>

---
