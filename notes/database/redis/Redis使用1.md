## Redis基本使用

### Redis基本配置
1. daemonize 是否是以守护进程的形式运行，推荐为yes
2. port redis实例的端口号，默认是6379
3. logfile redis系统日志的
4. dir redis的工作目录
* `config get * `获得当前所有配置

### Redis命令

#### Redis的通用命令
* keys *  #遍历数据库的特定的key值，后面是通配符
* dbsize #计算数据库的key总数
* exists key #检查key是否存在
* del key #删除数据库中对应key-value
* expire key seconds #设置key的过期时间，单位是s
* ttl key # 查看key剩余过期时间,过期后会返回-2表示已经删除
* persist key #去掉key的过期时间
* type key #会返回key的数据类型，如果不存在则会返回none

#### Redis的内部数据结构和编码

数据结构是应用于外部实现，而编码是对应于redis的内部具体实现，对于使用者是透明的。
#### 字符串--String
##### 基本命令
* get key  # 获取对应的键值
* set key value # 设置对应的键值，无论key是否存在都设置
* setnx key value # 设置对应的键值，key不存在才设置
* set key value  xx # 设置对应的键值，key存在才设置
* incr key # key自增1, 是一个原子操作，可以用作计数器
* decr key #key自减1
* incrby key num # key自增num
* decrby key num #key自减num
* mget key1 key2 ... # 批量获取key值，一次网络传输节约大量的网络时间
* getset key newvalue # 设置key为新的值，并且会返回旧的值
* append key value # 追加value到原来的值中
* strlen key # 返回字符串的字节长度

#### 哈希--Hash
key -- value(field--value)
##### 基本命令
* hget key field# 获取hash key对应的field的value
* hset key field value# 设置hash key对应的field的value
* hget key field # 删除hash key对应的field的value
* hexists key field # 判断对应的key是否有field对应的值
* hlen key # 返回hash的字节长度
* hmget key field1 field2# 批量获取hash key对应的field的value
* hmset key field1 value1 field2 value2# 批量设置hash key对应的field的value
* hgetall key # 返回hash key对应的所有的field和value
* hvals key #返回hash key对应的所有的value
* hkeys key #返回hash key对应的所有的key
* hdel key  field #删除key对应的field


#### 列表--list
key -- value(有序队列)
* 有序
* 可重复
* 队列

##### 基本命令
* rpush key value1 value2 ... # 从列表右边开始插入数据
* lpush key value1 value2 ... # 从列表左边开始插入数据
* linsert key before|after value newValue # 在key对应的列表的指定的值value前|后插入新的值
* lpop key # 弹出key对应的列表的左边第一个元素并返回 
* rpop key # 弹出key对应的列表的右边第一个元素并返回 
* lrem key count value # 根据参数 COUNT 的值，移除列表中与参数 VALUE 相等的元素,COUNT 的值可以是以下几种：
    * count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT 。
    * count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值。
  * count = 0 : 移除表中所有与 VALUE 相等的值。
* ltrim key start end # 按照索引范围截取子字符串
* lrange key start end #获取列表指定范围的所有的元素
* lindex key index #获取列表指定索引的元素
* llen key # 获取指定列表的长度，是元素的个数
* lset key index newValue # 设置指定索引的元素的值

#### 集合--set
key -- value(不允许重复)
* 无序
* 无重复
* 支持集合间操作

##### 基本命令
1. 集合内API
* sadd key element1  element2 ... # 向集合中添加元素，如果元素存在添加失败
* srem key element # 删除集合中元素
* scard key # 返回集合中元素的数量
* sismember key value # 判断value是否是在key对应的集合中
* srandmember key [count] # 随机返回key对应的集合中count个元素，仅仅返回随机元素，而不对集合进行任何改动。
* spop key # 于移除并返回集合中的一个随机元素
* smembers key # 返回key对应集合的所有的元素
* smembers 
2. 集合间API
* sdiff set1 set2 # 返回给集合之间的差集，即返回set1中存在，set2中不存在的元素，不存在的集合 key 将视为空集
* sinter set1 set2 # 返回给定所有给定集合的交集。 不存在的集合 key 被视为空集。 当给定集合当中有一个空集时，结果也为空集(根据集合运算定律)
* sunion set1 set2 # 返回给定集合的并集。不存在的集合 key 被视为空集
3. 使用场景

#### 有序集合--zset
key -- value(score--value)
* 无重复
* 有序

##### 基本命令
* zadd key score element(score是可以重复的，element是不能) #向集合中添加元素
* zrem key element #删除元素
* zscore key element # 获取对应元素的score
* zincrby key increScore element # 给指定元素的socore增加increScore, 并返回增加后的值
* zcard key # 返回集合元素的总个数
* zrank key element # 返回集合中element的排名
    * `zrank key 0 -1 withsocres` 返回集合中所有元素的得分
* zrange key start end [withsocres] # 返回指定索引范围的升序元素
* zrangebyscore key min max # 返回对应分数范围内的元素
* zcount key min max # 返回指定分数范围内元素的个数
* zremrangebyrank key start end # 删除指定排名范围的元素
* zremrangebyscore key min min # 删除指定分数范围的元素

##### 实战
可以用作排行榜



