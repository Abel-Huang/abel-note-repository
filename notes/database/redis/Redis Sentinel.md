## Redis Sentinel
为了解决Redis主从复制在节点出现故障时无法自行恢复，Redis提供了高可用的
解决方案Redis Sentinel。

### Redis Sentinel的介绍
* Redis Sentinel是Redis的高可用实现方案：故障发现、故障岗=自动转移、配置中心、客户端通知。
* 从Redis2.8开始可以正式使用，之前版本不可用。
#### Redis Sentinel的基本架构
Redis Sentinel是在原本的主从复制架构的系统中，增加了很多sentinel的节点，
可以认为sentinel是没有存放数据的Redis进程。  
客户端不会从Redis的master中获取数据，而是直接从sentinel中获取数据。

#### Redis Sentinel故障转移的工作流程
1. 多个sentinel发现并确认master出现问题
2. 选举出一个sentinel作为领导
3. 选出一个slave作为master
4. 通知其余salve成为新的master的slave
5. 通知客户端主从变化
6. 等待老的master复活成为新的master的slave

#### Redis Sentinel安装与配置
1. 配置开启主从节点，启动主从节点
    * `redis-server redis.conf`
2. 配置开启sentinel监控主节点(sentinel是特殊的redis, 默认端口是26379)
    * `redis-sentinel sentinel.conf`
3. 实际上应该是多台机器
4. 详细节点配置
   * 主节点的配置信息
   ```yml
    port 7000
    daemonize yes
    pidfile /xxx/xxx.pid
    logfile "xxxx.log"
    dir "/xxx/redis/data/"
   ```
   * 从节点的配置信息
   ```yml
    port 7001
    daemonize yes
    pidfile /xxx/xxx.pid
    logfile "xxxx.log"
    dir "/xxx/redis/data/"
    salveof 127.0.0.1 7000  // 主机地址
   ```
   * Sentinel节点的配置信息
   ```yml
    port 26379
    daemonize yes
    dir "/xxx/redis/data/"
    logfile "xxxx.log"
    sentinel monitor mymaster 127.0.0.1 7000 2 # 监控主节点的名称和主机名 超过两个sentinel认为主机有问题就会认为主机存在问题
    sentinel down-after-milliseconds mymaster 30000 # 认为主机出现问题的超时时间，单位为ms
    sentinel parallel-syncs mymaster 1 #
    sentinel failover-timeout mymaster 180000 # 故障转移时间
   ```
   
### Redis Sentinel和客户端的连接
#### 客户端连接的Redis Sentinel架构的基本步骤
1. 遍历Sentinel节点集合，获取一个可用的Sentinel节点
2. 返回master的节点信息
3. 客户端使用role或者role replication验证master节点信息
4. 通过发布订阅的方式，Redis客户端可以获取Redis数据节点的变化情况
#### 客户端连接的参数
1. Sentinel地址集合
2. masterName
3. 不是代理模式

##### 通过jedis连接
使用JedisSentinelPool 

##### 通过redis-py连接

### Redis Sentinel故障转移
#### Sentinel内部的三个定时任务
1. 每10s每个sentinel对master和slave执行info
    * 发现slave节点
    * 确认主从关系
2. 每2秒每个sentinel通过master节点的channel交换信息(pub/sub)
    * 通过_sentinel_:hello频道交互
    * 交互对节点的"看法"和自身信息
3. 每1秒每个sentinel对其他sentinel和redis执行ping 
    * 心跳检测，失败判定依据
    
#### 主观下线和客观下线
* 主观下线：每个sentinel节点对Redis节点失败的"偏见"
* 客观下线：所有sentinel节点对Redis节点失败的"共同意见"(超过quorum个统一， sentinel
最好是配置为奇数， quorum最好为sentinel数量的一半加1， sentinel的节点数量推荐至少为3)

#### Sentinel领导者选举
选举：
通过sentinel is-master-down-by-addr命令选举成为领导者
1. 每个做主观下线的Sentinel节点向其他Sentinel节点发送命令，要求将它设置为领导者
2. 收到命令的Sentinel节点如果没有同意通过其他Sentinel节点发送的命令， 那么将同意
该请求，否则拒绝
3. 如果该Sentinel节点发现自己的票数已经超过Sentinel集合半数且超过quorum，那么它将成为
领导者
4. 如果此过程中有多个Sentinel节点成为了领导者，那么将等待一段时间重新进行选举  

#### 故障转移
1. 从slave节点中选出一个"合适的"节点作为新的master节点
2. 对上面的slave节点执行slaveof no one命令让其成为master节点
3. 向剩余的slave节点发送命令，让他们成为新master节点的slave节点，复制规则和parallel-syncs
参数有关
4. 更新对原来master节点配置为slave，并保证着对其"关注"，当期故障回复后命令它去复制新的master节点

#### 怎么选择"合适的"节点
1. 优先选择slave-priority(slave节点优先级)最高的slave节点，如果存在则返回，不存在则继续下一步
2. 选择复制偏移量(offset)最大的slave节点(复制最完整),如果存在则返回，不存在则继续下一步
3. 选择runId最小的slave节点

### Redis Sentinel的运维问题
#### 节点运维
节点上线和下线：主节点、从节点、Sentinel节点
    * 机器下线:例如过保等情况
    * 机器性能不足，需要进行升级:如CPU、内存、硬盘、网络等
    * 节点自身故障：例如服务不稳定

#### 高可用读写分离
从节点的作用
1. 副本：高可用的基础
2. 扩展：读能力
    