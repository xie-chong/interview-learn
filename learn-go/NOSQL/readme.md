# redis常用操作命令

## String   
|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|`SET key value`设置指定 key 的值|`GET key`获取指定 key 的值。|`GETSET key value`将给定 key 的值设为 value ，并返回 key 的旧值(old value)。|  |
||||
|`MSET key value [key value ...]`同时设置一个或多个 key-value 对。|`MGET key1 [key2..]`获取所有(一个或多个)给定 key 的值。|`SETBIT key offset value`对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。|  |
||||
|`MSETNX key value [key value ...]`同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。|`GETRANGE key start end`返回 key 中字符串值的子字符|`SETRANGE key offset value`用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。|  |
||||
|`SETNX key value`只有在 key 不存在时设置 key 的值。|`STRLEN key`返回 key 所储存的字符串值的长度。|`PSETEX key milliseconds value`这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。|  |
||||
|`SETEX key seconds value`将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。|`GETBIT key offset`对 key 所储存的字符串值，获取指定偏移量上的位(bit)。|`INCR key`将 key 中储存的数字值增一。|  |
||||
|  |  |`INCRBY key increment`将 key 所储存的值加上给定的增量值（increment）。|  |
||||
|  |  |`INCRBYFLOAT key increment`将 key 所储存的值加上给定的浮点增量值（increment）。|  |
||||
|  |  |`DECR key`将 key 中储存的数字值减一。|  |
||||
|  |  |`DECRBY key decrementkey` 所储存的值减去给定的减量值（decrement）。|  |
||||
|  |  |`APPEND key value`如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾。|  |

---

## SET   
|  C  |  R  |  U  |  D  |
| :--------   | :--------   |  :--------   | :--------   |
|SADD key member1 [member2]向集合添加一个或多个成员     |SCARD key获取集合的成员数|SDIFFSTORE destination key1 [key2]返回给定所有集合的差集并存储在 destination 中|SMOVE source destination member将 member 元素从 source 集合移动到 destination 集合|
|  |SDIFF key1 [key2]返回给定所有集合的差集|SINTERSTORE destination key1 [key2]返回给定所有集合的交集并存储在 destination 中|SPOP key移除并返回集合中的一个随机元素|
|  |SINTER key1 [key2]返回给定所有集合的交集|SUNIONSTORE destination key1 [key2]所有给定集合的并集存储在 destination 集合中|SREM key member1 [member2]移除集合中一个或多个成员|
|  |SISMEMBER key member判断 member 元素是否是集合 key 的成员|  |  |
|  |SMEMBERS key返回集合中的所有成员|  |  |
|  |SRANDMEMBER key [count]返回集合中一个或多个随机数|  |  |
|  |SUNION key1 [key2]返回所有给定集合的并集|  |  |
|  |SSCAN key cursor [MATCH pattern] [COUNT count]迭代集合中的元素|  |  |

