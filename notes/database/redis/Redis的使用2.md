## Redis的高级使用

### 慢查询
慢查询日志就是系统在命令执行前后计算每条命令的执行时间,当超过预设阈值,就将这条命令的相关信息(例如:发生时间,耗时,命令的详细信息)记录到慢查询日志中
#### redis执行命令的生命周期
1. 客户端发送命令
2. 命令进行排队
3. redis server执行命令
4.server返回结果
其中慢查询发生在第三阶段，所以慢查询并不代表客户端没有超时，慢查询只是在于server执行复杂的命令。
#### 慢查询的配置
如果满足预设阈值(slowlog-log-slower-than)就将该命令记录在慢查询队列中，可以通过以下两个命令进行设置：
1. `slowlog-max-len`  慢查询队列的最大长
* 前进先出
* 超过最大长度的命令就会丢弃
* 默认值为128
2. `slowlog-log-slower-than`  执行时间超过这个值就被记录为慢查询
* slowlog-log-slower-than=0,将所有的命令记录为慢查询
* slowlog-log-slower-than < 0, 不记录任何记录
* 默认值为10000，单位us，即10ms
#### 慢查询的配置方式
1. 修改配置文件重启，不推荐
2. 慢查询命令,可以动态进行修改
* config get slowlog-log-slower-than 
* config set slowlog-log-slower-than newValue
* Redis Config rewrite 命令对启动 Redis 服务器时所指定的 redis.conf 配置文件进行改写
* slowlog get [n] # 获取慢查询队列
* slowlog len  # 获取慢查询队列长度
* slowlog reset # 清空慢查询队列

#### 慢查询的使用经验

### 流水线 -- pipeline
一次提交多个命令，在服务端执行时是按照顺序依次执行并按照顺序返回执行结果，可以大大节省网络请求消耗。
注意：
1. Redis的命令时间是us级别
2. pipeline每条次数都要控制(网络)
3. pipeline就是为了解决网络耗时情况
4. pipeline整体不是一个原子操作，里面的命令可能被拆分成多个命令，但是执行的相对顺序是不会改变的
5. 注意每次pipeline携带的数据量
6. pipeline每次只能作用在一个Redis节点上

### 发布订阅
Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。Redis 客户端可以订阅任意数量的频道。

#### 角色
* 发布者：发送消息，是一个Redis客户端
* 订阅者：接收消息，是一个Redis客户端
* 频道 ：是在Redis Server中 

#### 基本API
1. 发布
`publish channel message`

2. 订阅
` subscribe [channel] [channel]`
可以同时订阅一个或者多个频道

3. 取消订阅
` unsubscribe [channel] [channel]`

#### 发布订阅和消息队列的区别
发布订阅发布的消息只要订阅了这个频道的所有订阅者都可以接收到。
消息队列的点对点模式只能由一个订阅者接收到。

### 位图--bitmap
bitmap可以直接对位图中的位进行操作。
bitmap本质上是一个二进制字符数组
#### 基本API
* setbit key offset value # 给位图指定索引offset设置值，只能是0、1
* getbit key offset # 获取位图指定索引offset的值
* bitcount key [start end] # 获取位图指定范围内(单位是字节，注意不要错误理解成offset)的值为1的数量，没有设置start和end就是整个位图
* bitop op destkey key [key ...] # 对多个bitmap进行交集(and), 并集(or), 非(not)， 异或(xor)等操作，结果保存在destkey
* bitpos key targetBit [start] [end] # 获取位图指定范围内(单位是字节)第一个偏移量对应的值为targetBit的位置
#### 注意事项
1. type = string，基于String实现的，单个最大为512MB
2. 注意setbit时的偏移量
3. bitmap并不是在所有场景下都能剩下较多的内存

### HyperLogLog
基于HyperLogLog算法，可以使用极小的空间完成独立数量统计
本质还是字符串
对内存的使用量极低
#### 基本API
* pfadd key element [element] [element...] #向hyperloglog添加元素，一次可以添加多个元素
* pfcount key #计算hyperloglog的独立总数
* pfmerge destKey sourceKey [sourceKey...] # 合并多个hyperloglog

#### 使用经验
1. 有一定的错误率(官方给出的是0.81%)
2. 无法取出单条数据

### 地理位置信息--GEO 
存储经纬度，计算两地距离，范围计算等

#### 基本API
* geoadd key longitude latitude member [longitude latitude member  ...]添加地理位置信息，三个参数分别是精度，维度和标志位
* geopops key member # 获取存储的地理位置信息
* geodist key member1 member2 [unit] #计算两个地理位置之间的距离，单位m，km，mi(英里)，ft(英尺)



