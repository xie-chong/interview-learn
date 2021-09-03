
## [MySQL 中文文档](https://www.mysqlzh.com/)   


## mysql类似Oracle to_char()、to_date()函数的日期和字符相互转换方法
```
select sysdate() from dual;

date_format(date,'%Y-%m-%d') -------------->oracle中的to_char();
str_to_date(date,'%Y-%m-%d') -------------->oracle中的to_date();

    %Y：代表4位的年份
　　%y：代表2为的年份
　　%m：代表月, 格式为(01……12)
　　%c：代表月, 格式为(1……12)
　　%d：代表月份中的天数,格式为(00……31)
　　%e：代表月份中的天数, 格式为(0……31)
　　%H：代表小时,格式为(00……23)
　　%k：代表 小时,格式为(0……23)
　　%h： 代表小时,格式为(01……12)
　　%I： 代表小时,格式为(01……12)
　　%l ：代表小时,格式为(1……12)
　　%i： 代表分钟, 格式为(00……59)
　　%r：代表 时间,格式为12 小时(hh:mm:ss [AP]M)
　　%T：代表 时间,格式为24 小时(hh:mm:ss)
　　%S：代表 秒,格式为(00……59)
　　%s：代表 秒,格式为(00……59)
　　SELECT DATE_FORMAT(20130111191640,'%Y-%m-%d %H:%i:%s')
　　DATE_FORMAT(20130111191640,'%Y-%m-%d %H:%i:%s')
  ```
  
  ## mysql不能类似Oracle使用函数作为默认值
  错误示例：
  ```
  alter table stock
    add input_date_time VARCHAR(20) default date_format(sysdate(),'%Y-%m-%d %H:%i:%s') comment '录入时间';
  ```

## 查询mysql数据库中某个表存储数据的大小

```
select concat(round(sum(data_length/1024/1024),2),'MB') as data_length_MB,
       concat(round(sum(index_length/1024/1024),2),'MB') as index_length_MB,
       concat(round(sum((data_length + index_length)/1024/1024),2),'MB') as all_MB
from information_schema.tables
where table_schema='数据库名称' AND table_name='表名称';
```


https://blog.csdn.net/sumengnan/article/details/106564480

```
show VARIABLES like 'max_connections'

set GLOBAL max_connections=3600;

show VARIABLES like 'wait_timeout'

// 修改数据库连接超时时间sql语句：
//设置交互式连接超时时间，例如mysql客户端
set session interactive_timeout=28800;
set GLOBAL interactive_timeout=28800;
 
//设置非交互式连接超时时间，例如jdbc
set session wait_timeout=28800;
set GLOBAL wait_timeout=28800;
 
永久修改:mysql配置文件

[mysqld]
wait_timeout    = 28800
interactive_timeout    =  28800
#增加以上两列即可，因为官方文档要求修改此参数必须同时修改interactive_timeout

```


## 多索引，如何选择执行索引，会同时走多个索引吗？

[官网8.2.1.3 Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)

[参考](https://blog.csdn.net/molashaonian/article/details/107735359)

[参考](https://www.cnblogs.com/digdeep/p/4975977.html)

https://www.jianshu.com/p/34bd66629355


## Mysql 相邻两行记录某列的差值方法

按照借据号（due_bill_no）不同分组，然后分别求出相同due_bill_no相邻记录应还款日（term_due_date）的差值

```
select r1.due_bill_no,
       r1.term,
       r1.term_due_date,
       r1.due_bill_no,
       r2.term,
       r2.term_due_date,
       DateDiff(r2.term_due_date, r1.term_due_date) d_v
from (select (@rownum := @rownum + 1) as rownum,
             plan.due_bill_no,
             plan.term,
             plan.term_due_date
      from acc_repay.repayment_plan plan,
           (select @rownum := 0) r
      where project_no = 'WS10043190003'
      order by plan.due_bill_no, plan.term) r1
         left join (select (@index := @index + 1) as rownum,
                           plan.due_bill_no,
                           plan.term,
                           plan.term_due_date
                    from acc_repay.repayment_plan plan,
                         (select @index := 0) r
                    where project_no = 'WS10043190003'
                    order by plan.due_bill_no, plan.term) r2
                   on r1.due_bill_no = r2.due_bill_no
                       and r1.rownum = r2.rownum - 1;
```
