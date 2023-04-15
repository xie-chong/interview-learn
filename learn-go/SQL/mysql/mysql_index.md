# InnoDB 全文索引

InnoDB 是 MySQL 的一种存储引擎，支持全文索引功能。全文索引是一种用于快速搜索文本内容的索引方式，可以为指定的文本字段建立索引，从而提高文本搜索的效率。

**在 InnoDB 中创建全文索引，需要满足以下条件**：

1. 目标表必须使用 InnoDB 存储引擎。

2. 目标字段的数据类型必须是 TEXT、MEDIUMTEXT 或 LONGTEXT。

3. MySQL 配置文件中必须启用了 InnoDB 的全文索引功能。

**实现在 InnoDB 表上添加全文索引的步骤如下**：

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
