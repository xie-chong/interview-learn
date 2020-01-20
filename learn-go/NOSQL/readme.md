# redis常用操作命令

## String   
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


## Set   
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

## List
|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|LPUSHX key value|LINDEX key index|LSET key index value|LPOP key|
|RPUSH key value1 [value2]|LLEN key|LTRIM key start stop|RPOP key|
|LPUSHX key value|LRANGE key start stop|  |BLPOP key1 [key2 ] timeout|
|RPUSHX key value|  |  |BRPOP key1 [key2 ] timeout|
|LINSERT key BEFORE/AFTER pivot value|  |  |BRPOPLPUSH source destination timeout|
|  |  |  |RPOPLPUSH source destination|
|  |  |  |LREM key count value|

