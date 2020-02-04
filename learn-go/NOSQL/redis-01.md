# redis-01

<!-- MarkdownTOC -->

- [redis常用操作命令](#1)
  - [String](#1.1)
  - [List](#1.2)
  - [Hash](#1.3)
  - [Set](#1.4)
  - [sorted set](#1.5)
- [解析配置文件redis.conf](#2)


  
  
  
  
  
  
  
<!-- /MarkdownTOC -->

<h1 id="1">1. redis常用操作命令</h1>

<h2 id="1.1">1.1 String</h2>

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


<h2 id="1.2">1.2 List</h2>

|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|LPUSHX key value|LINDEX key index|LSET key index value|LPOP key|
|RPUSH key value1 [value2]|LLEN key|LTRIM key start stop|RPOP key|
|LPUSHX key value|LRANGE key start stop|  |BLPOP key1 [key2 ] timeout|
|RPUSHX key value|  |  |BRPOP key1 [key2 ] timeout|
|LINSERT key BEFORE/AFTER pivot value|  |  |BRPOPLPUSH source destination timeout|
|  |  |  |RPOPLPUSH source destination|
|  |  |  |LREM key count value|

<h2 id="1.3">1.3 Hash</h2>

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


<h2 id="1.4">1.4 Set</h2>

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



<h2 id="1.5">1.5 sorted set</h2>

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






---
<h1 id="2">2. 解析配置文件redis.conf</h1>

---

<h2 id="2.1">2.1 启动redis服务</h2>

```
# Note that in order to read the configuration file, Redis must be
# started with the file path as first argument:
#
# ./redis-server /path/to/redis.conf

```

* 配置文件路径  
```
/opt/redis-5.0.7/redis.conf
```

* redis安装路径   
```
/usr/local/redis/5.0.7/bin
```

* 启动服务   
```
./redis-server /opt/redis-5.0.7/redis.conf
```

以上路径会由于安装或存放，存在差异，仅供参考。


<h2 id="2.2">2.2 units单位</h2>

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


<h2 id="2.3">2.3 INCLUDES</h2>

```
################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# Notice option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
#
# include /path/to/local.conf
# include /path/to/other.conf

```   
和我们的Struts2配置文件类似，可以通过includes包含，redis.conf可以作为总闸，包含其他

<h2 id="2.4">2.4 GENERAL</h2>

```
################################# GENERAL #####################################

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
daemonize no

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised no

# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.
pidfile /var/run/redis_6379.pid

# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice

# Specify the log file name. Also the empty string can be used to force
# Redis to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null
logfile ""

# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no

# Specify the syslog identity.
# syslog-ident redis

# Specify the syslog facility. Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0

# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16

# By default Redis shows an ASCII art logo only when started to log to the
# standard output and if the standard output is a TTY. Basically this means
# that normally a logo is displayed only in interactive sessions.
#
# However it is possible to force the pre-4.0 behavior and always show a
# ASCII art logo in startup logs by setting the following option to yes.
always-show-logo yes

```   
* **daemonize**   
* **pidfile**   
* **port**   
* **tcp-backlog**   
设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。
在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值，所以需要确认增大somaxconn和tcp_max_syn_backlog两个值来达到想要的效果。   
* **timeout**   
* **bind**    
* **tcp-keepalive**  单位为秒，如果设置为0，则不会进行Keepalive检测，建议设置成60    
* **loglevel**   
* **logfile**   
* **syslog-enabled**  是否把日志输出到syslog中   
* **syslog-ident**  指定syslog里的日志标志   
* **syslog-facility**  指定syslog设备，值可以是USER或LOCAL0-LOCAL7   
* **databases**
































































