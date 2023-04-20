# COLLATE

**COLLATE是MySQL中用于控制字符比较和排序规则的关键字。它能够指定用于比较和排序的字符集以及相关的规则**。

在MySQL中，字符集是由一个或多个字符编码组成的集合，字符编码规定了如何将字符编码为二进制形式。当对字符串进行比较和排序时，需要先根据字符集和编码将字符串转换为二进制值，然后再进行比较和排序操作。而COLLATE则可以指定用于这个过程的字符集和规则。

COLLATE的语法为：

```
SELECT column1, column2,... FROM table_name ORDER BY column1 COLLATE collation_name;
```
其中，collation_name表示所使用的字符集和排序规则，可以指定系统提供的预定义的COLLATE，也可以自定义COLLATE。

```
CREATE TABLE `task`
(
    `task_id`   int(11) NOT NULL AUTO_INCREMENT COMMENT '自增主键',
    `task_type` int(11)                             DEFAULT NULL COMMENT '任务类型id',
    `task_rfid` varchar(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '关联外键1',
    PRIMARY KEY (`task_id`)
) ENGINE = InnoDB
  CHARSET = utf8
  COLLATE = utf8_unicode_ci COMMENT ='任务表';
```

MySQL中提供了多种字符集和排序规则。如果使用不同的字符集和排序规则进行比较和排序，可能会导致结果不一致。因此，在应用程序中使用COLLATE非常有必要，以确保字符串的正确比较和排序。

## 默认值

在MySQL中每种字符集都有一个默认排序规则。当在创建表时不指定COLLATE时，MySQL会使用默认的排序规则。如果在创建表时指定了COLLATE，则使用指定的排序规则。

可以使用SHOW COLLATION;命令查看MySQL可用的所有字符集和排序规则，可以使用SHOW VARIABLES LIKE 'character_set%';命令查看MySQL当前所使用的字符集。

当在查询中使用COLLATE时，如果没有指定排序规则，则使用当前字符集的默认排序规则。例如：

SELECT column1 FROM table1 ORDER BY column1 COLLATE utf8mb4_general_ci;
上述查询语句中，如果未指定排序规则，则使用当前字符集的默认排序规则，即utf8mb4_general_ci。

由于不同字符集具有不同的默认排序规则，因此需要特别注意在多个字符集之间进行比较和排序时可能存在的问题。建议在使用COLLATE时一定要指定排序规则，以避免出现不一致的结果。

## utf8和utf8mb4字符集

在MySQL中，utf8和utf8mb4字符集默认使用的排序规则为utf8_general_ci和utf8mb4_general_ci。这两个排序规则都是基于Unicode的字符编码来排序。

其中，utf8_general_ci适用于多种语言，包括拉丁语系（如英文、法语、德语等）、希腊语、泰语、越南语等；而utf8mb4_general_ci还包括了Emoji表情等更多特殊字符的支持。

需要注意的是，虽然utf8_general_ci和utf8mb4_general_ci默认排序规则在绝大多数情况下都能够满足需求，但对于某些特殊的应用场景可能并不适用。因此，在进行字符比较和排序时，建议根据实际情况选择合适的排序规则。如果默认排序规则不满足需求，则可以使用自定义COLLATE指定所需的排序规则。

例如，要使用utf8_general_cs排序规则（区分大小写），可以使用以下语句：
```
SELECT column1 FROM table1 ORDER BY column1 COLLATE utf8_general_cs;
```
