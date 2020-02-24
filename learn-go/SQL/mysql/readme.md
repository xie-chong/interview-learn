
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
