## Redis持久化
1. 什么是redis持久化
redis所有的数据保存在内存中，对数据的更新将异步的保存到磁盘上
2. redis持久化的方式
持久化主要有两种方式，快照，将某个时刻的数据全部记录下来
写日志，将相关的命令记录到日志中，可以通过日志进行数据的恢复
主要是RDB(快照)和AOF(日志)两种持久化方式。

### RDB
redis将内存中的数据创建一个二进制的RDB文件保存在硬盘中进行持久化。

#### 三种方式
1. `save ` # 同步
客户端发送一个save命令，就会创建一个rdb文件作为快照，会阻塞客户端命令，在数据量很大的时候会对线上环境造成比较大的影响
2. `bgsave` # 异步
客户端发送一个bgsave命令，redis server会fork一个子进程进行复制，不会阻塞(fork过程会造成一定的阻塞)，但是需要消耗更多内存
3. 自动生成RDB
当满足配置的条件(下面三个条件其中的一个满足就可以)后会自动通过bgsave创建一个RDB文件
默认配置(左边)和最佳配置(右边)

dbfilename 表示生成的RDB快照文件名
dir 表示生成RDB快照文件的路径，推荐在一个比较大的文件位置
stop-writes-on-bgsave-error 表示遇到错误是否需要继续写入
rdbcompression 表示RDB文件是否需要开启文件压缩
rdbchecksum 表示RDB文件是否需要开启文件校验和

#### 触发机制
在没有以上三种方式时以下情况也会触发RDB文件的复制
1. 全量复制
2. debug reload
3. shutdown

#### RDB缺陷
1. 耗时，耗性能(fork, 有可能阻塞)
2. 不可控，容易丢失数据(宕机会使没有备份的数据丢失)

### AOF
Redis执行修改数据的命令会保存到AOF文件，可以通过AOF文件进行文件的修改
#### AOF的三种策略
1. always
将每条命令都写入到文件中。
不丢失数据，但是IO消耗较大

2. everysec
每秒一次fsync到AOF文件中
如果出现故障，可能会丢失一秒钟的数据
可以有效地保护磁盘
reddis默认值，推荐

3. no
由操作系统确定写入的时机
用户不可控
一般不会使用

#### AOF重写
1. 减少硬盘占用量
2. 加速恢复速度

实现方式：
1. bgrewriteaof
fork出子进程然后执行AOF重写，针对内存中的数据进行一次重写
2. AOF重写配置
配置：
auto-aof-rewrite-min-size AOF文件重写需要的尺寸
auto-aof-rewrite-percentage AOF文件增长率
统计：
aof_current_size AOF当前的尺寸
aof_base_size AOF上次启动和重写的尺寸

自动触发时机：
* aof_current_size  > auto-aof-rewrite-min-size
*  (aof_current_size - aof_base_size)/aof_base_size  > auto-aof-rewrite-percentag
只有满足上述两个条件时才会发生自动触发AOF

#### 配置
1

#### RDB和AOF的选择



### 常见问题
####  fork操作
* 同步操作
* 与内存量息息相关
* info查看耗时

1. 改善fork
* 优先使用 物理机
* 控制Redis实例的最大可用内存
* 合理配置Linux内存的分配策略
* 降低fork的频率：如放宽AOF重写自动触发机制

#### 子进程开销和优化
1. CPU开销
RDB和AOF文件生成是CPU密集型
2. 内存
fork内存开销
3. 硬盘

#### AOF追加阻塞

