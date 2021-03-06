# <p align="center">NoSQL常用操作命令</p>

<!-- MarkdownTOC -->

- [1. redis常用操作命令](#1)   
  - [String](#1.1)   
  - [List](#1.2)   
  - [Hash](#1.3)   
  - [Set](#1.4)   
  - [sorted set](#1.5)   
- [2. redis经验积累](#2)    
- [3. 学习网址](#3)    
  
  
  
  
  
  
<!-- /MarkdownTOC -->

<h1 id="1">1. redis常用操作命令</h1>

<h2 id="1.1">String</h2>

|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|SET key value|GET key|GETSET key value|  |
|MSET key value [key value ...]|MGET key1 [key2..]|SETBIT key offset value|  |
|MSETNX key value [key value ...]|GETRANGE key start end|SETRANGE key offset value|  |
|SETNX key value|STRLEN key|PSETEX key milliseconds value|  |
|SETEX key seconds value|GETBIT key offset|INCR key|  |
|  |  |INCRBY key increment|  |
|  |  |INCRBYFLOAT key increment|  |
|  |  |DECR key|  |
|  |  |DECRBY key decrementkey|  |
|  |  |APPEND key value|  |  |


<h2 id="1.2">List</h2>

|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|LPUSHX key value|LINDEX key index|LSET key index value|LPOP key|
|RPUSH key value1 [value2]|LLEN key|LTRIM key start stop|RPOP key|
|LPUSHX key value|LRANGE key start stop|  |BLPOP key1 [key2 ] timeout|
|RPUSHX key value|  |  |BRPOP key1 [key2 ] timeout|
|LINSERT key BEFORE/AFTER pivot value|  |  |BRPOPLPUSH source destination timeout|
|  |  |  |RPOPLPUSH source destination|
|  |  |  |LREM key count value|

<h2 id="1.3">Hash</h2>

|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|HMSET key field1 value1 [field2 value2 ]|HEXISTS key field|HINCRBY key field increment|HDEL key field1 [field2]|
|HSET key field value|HGET key field|HINCRBYFLOAT key field increment|  |
|HSETNX key field value|HMGET key field1 [field2]|  |  |
|  |HGETALL key|  |  |
|  |HKEYS key|  |  |
|  |HVALS key|  |  |
|  |HLEN key|  |  |
|  |HSCAN key cursor [MATCH pattern] [COUNT count]|  |  |  |


<h2 id="1.4">Set</h2>

|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|SADD key member1 [member2]|SCARD key|SDIFFSTORE destination key1 [key2]|SMOVE source destination |
|  |SDIFF key1 [key2]|SINTERSTORE destination key1 [key2]|SPOP key|
|  |SINTER key1 [key2]|SUNIONSTORE destination key1 [key2]|SREM key member1 [member2]|
|  |SISMEMBER key member|  |  |
|  |SMEMBERS key|  |  |
|  |SRANDMEMBER key [count]|  |  |
|  |SUNION key1 [key2]|  |  |
|  |SSCAN key cursor [MATCH pattern] [COUNT count]|  |  |



<h2 id="1.5">sorted set</h2>

|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|ZADD key score1 member1 [score2 member2]|ZCARD key|ZINCRBY key increment member|ZREM key member [member ...]|
|  |ZCOUNT key min max|ZINTERSTORE destination numkeys key [key ...]|ZREMRANGEBYLEX key min max|
|  |ZLEXCOUNT key min max|ZUNIONSTORE destination numkeys key [key ...]|ZREMRANGEBYRANK key start stop|
|  |ZRANGE key start stop [WITHSCORES]|  |	ZREMRANGEBYSCORE key min max|
|  |ZRANGEBYLEX key min max [LIMIT offset count]|  |  |
|  |ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT]|  |  |
|  |ZRANK key member|  |  |
|  |ZREVRANGE key start stop [WITHSCORES]|  |  |
|  |ZREVRANGEBYSCORE key max min [WITHSCORES]|  |  |
|  |ZREVRANK key member|  |  |
|  |ZSCORE key member|  |  |
|  |ZSCAN key cursor [MATCH pattern] [COUNT count]|  |  |






  
  
  
 

<h1 id="2">2. redis经验积累</h1>

<h2 id="2.1">2.1 场景：清除redis缓存</h2>

1. JDBC表数据缓存到redis，如果手动修改了该表的数据，请注意清除redis缓存

2. redis中查看数据库的个数 ```config get databases```












<h1 id="3">3. 学习网址</h1>

1. [Redis 命令参考](http://redisdoc.com/topic/index.html)   

