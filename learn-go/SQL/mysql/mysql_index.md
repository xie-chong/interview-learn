# InnoDB 全文索引

InnoDB 是 MySQL 的一种存储引擎，支持全文索引功能。全文索引是一种用于快速搜索文本内容的索引方式，可以为指定的文本字段建立索引，从而提高文本搜索的效率。

## 在 InnoDB 中创建全文索引，需要满足以下条件：

1. 目标表必须使用 InnoDB 存储引擎。

2. 目标字段的数据类型必须是 TEXT、MEDIUMTEXT 或 LONGTEXT。

3. MySQL 配置文件中必须启用了 InnoDB 的全文索引功能。

## 实现在 InnoDB 表上添加全文索引的步骤如下：

1. 修改 MySQL 的配置文件，在 [mysqld] 段中加入以下语句：
```
innodb_ft_enable_stopword=ON
innodb_ft_server_stopword_table=stopwords
```
其中，第一个参数启用 InnoDB 的全文索引功能，第二个参数指定停止词表的名称。

2. 在目标表上创建全文索引。可以通过 ALTER TABLE 语句来实现。例如，在表 t1 的字段 c1 上创建全文索引，可以执行以下命令：
```
ALTER TABLE t1 ADD FULLTEXT INDEX ft_c1 (c1);
```
其中，ft_c1 是索引名称，c1 是要建立索引的字段名。

3. 进行全文检索操作。可以使用 MATCH AGAINST 语句进行全文检索。例如，查找 t1 表中包含关键词“hello”的记录，可以执行以下语句：
```
SELECT * FROM t1 WHERE MATCH (c1) AGAINST ('hello');
```

需要注意的是，InnoDB 的全文索引与 MyISAM 引擎的全文索引有所不同。MyISAM 引擎的全文索引支持 BOOLEAN 模式和 NATURAL LANGUAGE 模式，而 InnoDB 的全文索引仅支持 BOOLEAN 模式。在进行全文检索时，如果需要使用 BOOLEAN 模式，请在 MATCH AGAINST 语句中添加 IN BOOLEAN MODE 子句。

总的来说，在 InnoDB 表上创建和使用全文索引需要相应的配置和调优，才能达到最佳性能。同时，全文索引也有一定的限制，例如不支持短词搜索、不支持前缀搜索等，需要根据具体需求选择合适的方案。


## InnoDB 的全文索引 BOOLEAN 模式

InnoDB 引擎的全文索引支持 BOOLEAN 模式，是一种用于实现高级文本搜索的模式。通过 BOOLEAN 模式，可以使用 AND、OR、NOT、+、- 等符号对检索条件进行组合，从而实现更灵活、更精确的搜索方式。

**具体来说，BOOLEAN 模式支持以下操作符**：

1. AND：表示取交集，只返回包含所有关键词的记录。

2. OR：表示取并集，返回包含任意一个关键词的记录。

3. NOT：表示排除操作，返回不包含指定关键词的记录。

4. +：表示必须包含指定关键词，相当于 AND 操作。

5. -：表示必须不包含指定关键词，相当于 NOT 操作。

在 MATCH AGAINST 语句中使用 BOOLEAN 模式，需要在关键词前后分别添加引号，如下所示：
```
SELECT * FROM mytable WHERE MATCH (mycolumn) AGAINST ('"keyword1" +keyword2 -keyword3' IN BOOLEAN MODE);
```
在这个例子中，使用了双引号将 keyword1 包裹起来，表示必须完全匹配该关键词；使用加号将 keyword2 前缀，表示必须包含该关键词；使用减号将 keyword3 前缀，表示不能包含该关键词。

需要注意的是，在 BOOLEAN 模式下，全文索引会忽略一些常见的词汇和符号，称为停止词(stopwords)，例如 the、a、an、in 等。如果需要搜索这些停止词，可以在 MySQL 配置文件中修改相应的参数。

总的来说，BOOLEAN 模式可以让用户更加灵活、精确地进行文本搜索，提高全文检索的效率和准确率。但同时也需要注意，在使用 BOOLEAN 模式时要注意搜索条件的组合方式，避免出现错误或者漏洞，同时还需要根据实际情况选择合适的查询方式，以达到最佳性能。


## 自定义的停止词列表

停止词(stopwords)是指在全文搜索中被忽略的一些常见词汇和符号，例如 the、a、an、in 等。这些词汇在文本中出现的频率很高，但对于文本的含义和关键信息贡献较小，因此在全文索引中被排除掉，从而提高索引效率和检索准确率。

使用停止词可以有效减少全文索引的大小和搜索的时间，避免将无用的信息加入到索引中，并且可以降低误差率和查询开销。同时，由于不同语言中的停止词可能不同，因此在使用全文搜索时需要根据具体情况调整停止词列表。

在 MySQL 中，InnoDB 引擎的全文索引使用的停止词列表可以通过配置文件中的参数进行设置和修改。例如，innodb_ft_server_stopword_table 参数可以指定使用的停止词列表名称，该列表包含了一些默认的停止词和用户定义的停止词。

需要注意的是，在某些情况下，停止词可能会影响文本搜索的效果和准确性，例如在搜索包含停止词的短语或专业术语时。因此，在使用全文搜索时需要综合考虑停止词的使用与否，并根据实际情况决定是否需要添加或移除停止词，以达到最佳的搜索效果。

**自定义停止词列表步骤**

1. 创建一个包含停止词的文本文件，每行一个词。

2. 将该文件上传到 MySQL 服务器上的某个目录中，例如 /var/lib/mysql-files。

3. 在 MySQL 中创建一个新的停止词列表，并指定该列表使用的文件路径及格式。例如，可以使用以下 SQL 语句创建一个名为 my_stopwords 的列表：
```
CREATE TABLE my_stopwords (word VARCHAR(50)) 
ENGINE = InnoDB
ROW_FORMAT = DYNAMIC
DATA DIRECTORY = '/var/lib/mysql-files/'
INDEX DIRECTORY = '/var/lib/mysql-files/'
TABLESPACE ts10
STOPWORDS = "FILE '/var/lib/mysql-files/stopwords.txt'"; 
```
在这个例子中，使用 STOPWORDS 子句指定了停止词列表的文件路径。

4. 修改 innodb_ft_server_stopword_table 参数的值，指定使用自定义的停止词列表。例如，可以使用以下 SQL 语句将其设置为 my_stopwords：
```
SET GLOBAL innodb_ft_server_stopword_table = my_stopwords;
```

需要**注意**的是，停止词列表文件的编码必须与 MySQL 数据库的字符集相同，否则可能导致词汇解析错误或乱码等问题。同时，修改 innodb_ft_server_stopword_table 参数的操作只对新建的全文索引有效，已经存在的全文索引仍然使用原来的停止词列表。
