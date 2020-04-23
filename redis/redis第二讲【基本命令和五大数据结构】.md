## 【二、Redis基本命令和五大数据结构】

#### redis的基础知识和命令

- redis 是一个单进程（包装epoll函数来对读写事件进行相应）
- 默认有16个数据库，初始使用的数据库为0号库
- 默认端口为6379
- select ：切换数据库
- dbsize：查看当前库的key的个数
- flushdb：清空当前库
- flushall：清空所有库

#### redis的五大数据类型

###key键

- keys *：查看当前所有库的key

```shell
127.0.0.1:6379> keys *
1) "k1"
2) "k2"
```

- key xxx*：查看以xxx开头的key

```shell
127.0.0.1:6379> keys k*
1) "k1"
2) "k2"
```

- exists key：判断key是否存在；存在返回1；不存在返回0  

```shell
127.0.0.1:6379> exists k1 #存在
(integer) 1
127.0.0.1:6379> exists k3 #不存在
(integer) 0
```

- move key db：将某个key移动到某个db库，成功返回1失败为0  

```shell
127.0.0.1:6379> move k1 1
(integer) 1
127.0.0.1:6379> get k1 #因为k1被移动到1号库所以查询不到
(nil)
127.0.0.1:6379> move k1 1
(integer) 0
```

- expire key：设置过期时间（单位是秒）

```shell
127.0.0.1:6379> expire k2 10
(integer) 1
```

- persist key：移除过期时间变成永不过期，成功返回1

```shell
127.0.0.1:6379> expire k1 500
(integer) 1
127.0.0.1:6379> ttl k1
(integer) 497
127.0.0.1:6379> persist k1
(integer) 1
127.0.0.1:6379> ttl k
(integer) -1
```

- ttl key：查看key的过期状态，-1永不过期，-2已经过期

```shell
127.0.0.1:6379> expire k2 10
(integer) 1
127.0.0.1:6379> ttl k2
(integer) 7
127.0.0.1:6379> ttl k2
(integer) -2
127.0.0.1:6379> ttl k1
(integer) -1
```

- del key ：删除key

```shell
127.0.0.1:6379> del k1
(integer) 1
```

- type key：返回key的类型

```shell
127.0.0.1:6379> type k2
string
```

### string

- set:添加    

```shell
127.0.0.1:6379> set k1 v1
OK
```

- get：获得     

```shell
127.0.0.1:6379> get k1
"v1"
```

- del：删除     

```shell
127.0.0.1:6379> del k1
(integer) 1
```

- append key xxx：往key后面添加xxx    

```shell
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> append k1 123456
(integer) 8
127.0.0.1:6379> get k1
"v1123456"
```

- strlen：获取key的长度     

```shell
127.0.0.1:6379> strlen k1
(integer) 8
```

- Incr key：累加    只能是数字相加减不然会报错
- decr key：累减    

```shell
127.0.0.1:6379> lncr k1
(error) ERR unknown command `lncr`, with args beginning with: `k1`, 
127.0.0.1:6379> set k2 11
OK
127.0.0.1:6379> INCR k2
(integer) 12
127.0.0.1:6379> decr k2
(integer) 11
```

- incrby key n：累加n    
- decrby key n：累减n     

```shell
127.0.0.1:6379> set k2 11
OK
127.0.0.1:6379> incrby k2 9
(integer) 20
127.0.0.1:6379> decrby k2 10
(integer) 10
```

- getrange key 0 -1 获取key的范围值 0 -1 表示全长的值     

```shell
127.0.0.1:6379> getrange k1 0 2 #获取下标0-2
"v11"
127.0.0.1:6379> getrange k1 0 -1 #获取总长度
"v1123456"

```

- setrange key n xxx 更换key下标以n开始后面的值    

```shell
127.0.0.1:6379> setrange k1 0 54321
(integer) 8
127.0.0.1:6379> get k1
"54321456"
```

- mset ：批量添加    
- mget：批量查找    
- msetnx：批量添加很多值 只要存在其中一个key，所有的key都会存不上  

```shell
127.0.0.1:6379> mset k3 k3 k4 v4
OK
127.0.0.1:6379> mget k3 k4
1) "k3"
2) "v4"
127.0.0.1:6379> msetnx k5 v3 k4 v4
(integer) 0
127.0.0.1:6379> mget k4 k5
1) "44"
2) (nil)
```

- getset：先取值在设置值

```shell
127.0.0.1:6379> getset k5 v5
(nil)
127.0.0.1:6379> getset k4 v5
"44"
127.0.0.1:6379> get k4
"v5"
```

###list（简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边））

- lpush：反添加     
- rpush：正添加     
- lrange：查看 

```shell
127.0.0.1:6379> lpush k1 1 2 3 4 5 
(integer) 5
127.0.0.1:6379> LRANGE k1 0 -1
1) "5"
2) "4"
3) "3"
4) "2"
5) "1"
127.0.0.1:6379> rpush k2 1 2 3 4 5 
(integer) 5
127.0.0.1:6379> LRANGE k2 0 -1
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"

```

- lpop：弹出最上面的key-value     
- rpop：弹出最下面的key-value     
- lindex：按照索引下标获取元素 
- llen：常看长度     

```shell
127.0.0.1:6379> LPOP k1 
"5"
127.0.0.1:6379> RPOP k1
"1"
127.0.0.1:6379> LRANGE k1 0 -1 
1) "4"
2) "3"
3) "2"
127.0.0.1:6379> lindex k1 0
"4"
127.0.0.1:6379> LRANGE k1 0 -1 
1) "4"
2) "3"
3) "2"
127.0.0.1:6379> llen k1
(integer) 3

```

- lrem key：删除n个value     

```shell
127.0.0.1:6379> LRANGE k1 0 -1 
 1) "3"
 2) "2"
 3) "1"
127.0.0.1:6379> lrem k1 3 3 
(integer) 1
127.0.0.1:6379> LRANGE k1 0 -1 
1) "2"
2) "1"
```

- ltrim key 开始index 结束index ：截取指定范围的值后再给当前key赋值     

```shell
127.0.0.1:6379> LRANGE k1 0 -1 
1) "2"
2) "1"
3) "2"
4) "1"
127.0.0.1:6379> ltrim k1 0 1 #将0-1的值从新赋值给这个key
OK
127.0.0.1:6379> LRANGE k1 0 -1 
1) "2"
2) "1"

```

- rpoplpush 源列表  目的列表：把元列表的最后的值放在目的列表的最上面     

```shell
127.0.0.1:6379> LRANGE k1 0 -1  #k1集合
1) "2"
2) "1"
127.0.0.1:6379> RPOPLPUSH k1 k2 #将k1的第一个值给了k2
"1"
127.0.0.1:6379> LRANGE k2 0 -1 #查看k2的值
1) "1"
2) "1"
3) "2"
127.0.0.1:6379> LRANGE k1 0 -1 #查看k1的值
1) "2"
```

- lset key index value ： 替换key当前下标的值     

```shell
127.0.0.1:6379> LRANGE k1 0 -1
1) "2"
127.0.0.1:6379> lset k1 0 555555555
OK
127.0.0.1:6379> LRANGE k1 0 -1
1) "555555555"
```

- linsert key  beforre/after 值1  值2 ：在具体值得前面或者后面添加value值，不成功返回-1

```shell
127.0.0.1:6379> LRANGE k1 0 -1
1) "555555555"
127.0.0.1:6379> LINSERT k1 before 555555555 111 #在具体值前添加
(integer) 2
127.0.0.1:6379> LINSERT k1 after  555555555 111 #在具体值后添加
(integer) 3
127.0.0.1:6379> LINSERT k1 before 5555 111 #添加失败
(integer) -1
127.0.0.1:6379> LRANGE k1 0 -1
1) "111"
2) "555555555"
3) "111"

```

###hash （一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。）

- hset：添加     
- hget：查找     
- hdel：删除     

```shell
127.0.0.1:6379> hset baby id 1
(integer) 1
127.0.0.1:6379> hget baby id
"1"
127.0.0.1:6379> del baby id
(integer) 1
```

- hmset ：批量添加 一个key的多个属性
- hmget：批量查找key的多个属性值
- hkeys：查看所有的key 
- hvals：常看所有的values  
- hgetall：批量查看所有的key-value

```shell
127.0.0.1:6379> hmset baby id 1 name zhangsan age 11
OK
127.0.0.1:6379> hmget baby id name 
1) "1"
2) "zhangsan"
127.0.0.1:6379> hkeys baby
1) "id"
2) "name"
3) "age"
127.0.0.1:6379> hvals baby
1) "1"
2) "zhangsan"
3) "11"
127.0.0.1:6379> hgetall baby
1) "id"
2) "1"
3) "name"
4) "zhangsan"
5) "age"
6) "11"
```

- hlen：查看有几个属性
- hexists key： 查看是否存在某个属性值，有返回1，没有返回0
- hincrby：往某个key的value的后面累加数字  只用于数字 ，其他会报错
- hsetnx：不存在就赋值 存在就返回0 添加失败

```shell
127.0.0.1:6379> hlen baby
(integer) 3
127.0.0.1:6379> HEXISTS baby id 
(integer) 1
127.0.0.1:6379> HEXISTS baby id1
(integer) 0
127.0.0.1:6379> HINCRBY baby id 1111
(integer) 1112
127.0.0.1:6379> HINCRBY baby name  1111
(error) ERR hash value is not an integer
127.0.0.1:6379> hsetnx baby id 1
(integer) 0
127.0.0.1:6379> hsetnx baby id1 1
(integer) 1
```

###set（String 类型的无序不可重复集合。通过哈希表实现的）

- sadd ：添加     
- smembers：查看     
- sismember：查看key里面纯在某个元素    
- scard：获取集合里的元素个数     
- srandmember key n: 随机出n个数     
- srem key value ： 删除集合中的元素     

```shell
127.0.0.1:6379> sadd score 1 2 3 4   #添加     
(integer) 6
127.0.0.1:6379> smembers score #查看     
1) "1"
2) "2"
3) "3"
4) "4"
127.0.0.1:6379> scard score #获取集合里的元素个数     
(integer) 4
127.0.0.1:6379> sismember score 2 #查看key里面纯在某个元素    
(integer) 1
127.0.0.1:6379> srandmember score 3 #随机出n个数    
1) "1"
2) "2"
3) "4"
127.0.0.1:6379> srem score 3 4  #删除集合中的元素     
(integer) 2
127.0.0.1:6379> smembers score
1) "1"
2) "2"
```

- spop key  n： 随机出栈 n个 

```shell
127.0.0.1:6379> spop b2 3
1) "1"
2) "2"
3) "4"
```

- smove key1 key2  member：将key1中的member移动到key2    

```shell
127.0.0.1:6379> SMOVE b1 b2 3
(integer) 1
127.0.0.1:6379> SMEMBERS b2
1) "1"
2) "3"
```

- sdiff：差集     
- sinter：交集     
- sunion：并集

```shell
127.0.0.1:6379> sadd b1 1 2 3 
(integer) 3
127.0.0.1:6379> sadd b2 1 2 4
(integer) 3
127.0.0.1:6379> sdiff b1 b2 #差集
1) "3"
127.0.0.1:6379> sinter b1 b2 #交集
1) "1"
2) "2"
127.0.0.1:6379> sunion b1 b2 #并集
1) "1"
2) "2"
3) "3"
4) "4"
```

###zset（有序的不可重复列表。每个元素关联一个可重复分数score。通过score进行排序。）

- zrange：通过索引区间返回有序集合成指定区间内的成员
- zrangebyscore：通过分数返回有序集合指定区间内的成员
- zcard：获取有序集合的成员数
- zcount：计算在有序集合中指定区间分数的成员数

```shell
127.0.0.1:6379> zadd zset 1 q 2 w 3 e #向zset添加数据
(integer) 3
127.0.0.1:6379> zrange zset 0 -1 #查询zset全部的数据
1) "q"
2) "w"
3) "e"
127.0.0.1:6379> zrangebyscore zset 1 2  #根据分数范围查找数据
1) "q"
2) "w"
127.0.0.1:6379> zcard zset #获取zset的成员数
(integer) 3
127.0.0.1:6379> zcount zset 1 2  #根据分数范围获取值的个数
(integer) 2
```

- zrem：移除有序集合中的一个或多个成员
- zrevrange ：在score范围内按照score从大到小返回数据

```shell
127.0.0.1:6379> zrevrange zset 1 2 withscores
1) "w"
2) "2"
3) "4"
4) "1.3"
```

- zrevrangebyscore：移除有序集合中给定的分数区间的所有成员
- ZRANK key ：返回有序集合中指定成员的索引

```shell
127.0.0.1:6379> zrank zset w #返回w在集合中的索引
(integer) 4
```

- ZSCORE key member：返回有序集中成员的分数值

```shell
127.0.0.1:6379> zscore zset 4
"1.3"
```

关于更多的命令指示我们从这个网上去查看http://redisdoc.com/

下一节我们讲一下**Redis的持久化之RDB**

关注公众号 果咩z  继续学习redis和其他知识

