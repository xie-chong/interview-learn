## redis常用操作命令

| C        | R   |  U  |  D  |
| :--------:   | :--------:   |  :--------:   | :--------:   |
| SET | SET | SET | SET |
|SADD key member1 [member2]向集合添加一个或多个成员     |SCARD key获取集合的成员数|SDIFFSTORE destination key1 [key2]返回给定所有集合的差集并存储在 destination 中|SMOVE source destination member将 member 元素从 source 集合移动到 destination 集合|
|  |SDIFF key1 [key2]返回给定所有集合的差集|SINTERSTORE destination key1 [key2]返回给定所有集合的交集并存储在 destination 中|SPOP key移除并返回集合中的一个随机元素|
|  |SINTER key1 [key2]返回给定所有集合的交集|SUNIONSTORE destination key1 [key2]所有给定集合的并集存储在 destination 集合中|SREM key member1 [member2]移除集合中一个或多个成员|
|  |SISMEMBER key member判断 member 元素是否是集合 key 的成员|  |  |
|  |SMEMBERS key返回集合中的所有成员|  |  |
|  |SRANDMEMBER key [count]返回集合中一个或多个随机数|  |  |
|  |SUNION key1 [key2]返回所有给定集合的并集|  |  |
|  |SSCAN key cursor [MATCH pattern] [COUNT count]迭代集合中的元素|  |  |

