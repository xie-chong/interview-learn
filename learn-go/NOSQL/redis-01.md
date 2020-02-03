# redis常用操作命令

<!-- MarkdownTOC -->

- [redis常用操作命令](#1)
  - [String](#1.1)
  - [List](#1.2)
  - [Hash](#1.3)
  - [Set](#1.4)
  - [sorted set](#1.5)
- [解析配置文件redis.conf](#2)


  
  
  
  
  
  
  
<!-- /MarkdownTOC -->

<h1 id="1">redis常用操作命令</h1>

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






<h1 id="2">解析配置文件redis.conf</h1>

**启动redis服务**   
```
# Note that in order to read the configuration file, Redis must be
# started with the file path as first argument:
#
# ./redis-server /path/to/redis.conf

```

**配置文件路径**  
```
/opt/redis-5.0.7/redis.conf
```

**redis安装路径**   
```
/usr/local/redis/5.0.7/bin
```

**启动服务**   
```
./redis-server /opt/redis-5.0.7/redis.conf
```

<h2 id="2.1">units单位</h2>

```
# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.
```

1. 配置大小单位,开头定义了一些基本的度量单位，只支持bytes，不支持bit   
2. 对大小写不敏感









































































