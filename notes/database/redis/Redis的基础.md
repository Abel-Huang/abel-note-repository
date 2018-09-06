### Redis的特性
* 速度快
    * 内存
* 持久化
    * Redis所有的数据保存在内存中，对数据的更新会异步保存在磁盘中
* 多种数据结构
    * 字符串， Hash表， 链表， 集合， 有序集合
    * BitMaps(位图)， HyperLogLog(超小内存唯一值计数)，GEO(地理信息定位)  
* 支持多种编辑语言
* 功能丰富
    * 发布订阅， Lua脚本， 简单事务，pipeline
* 简单
    * 代码精简， 不依赖外部库，单线程
* 主从复制
* 高可用、分布式
    *Redis-Sentinel(v2.8)支持高可用
    *Redis-Cluster(v3.0)支持分布式

### Redis的应用场景
* 缓存系统
* 计数器
* 消息队列系统
* 排行榜

### 安装
#### Redis安装方法
* 下载安装文件 `wget xxxxxxxxxxxxx`
* 解压缩 `tar -xzf xxxxxx`
* 编译 `make&& make install`

#### 主要的可执行文件
* redis-server redis服务端
* redis-cli redis客户端，命令行工具
* redis-benchmark 基准测试工具
* redis-check-aof AOF修复工具
* redis-check-dump RDB修复工具
* redis-sentinel Sentinel服务器

#### 启动方法
* 默认直接启动
    * `redis-server` 命令即可启动
* 动态参数启动
    * `redis-server --port 6380` 动态设置以6380作为端口进行启动
* 配置文件启动
    * `redis-server redis.config` 以 redis.config作为配置文件
    * 推荐生产环境使用
* 客户端连接方式
    *  `redis-cli -h 106.14.173.42 -p 6379`
    * `auth password` 输入登录密码
* 客户端消息提示
    * 状态回复
    * 错误回复
    * 整数回复
    * 字符串回复
    * 多字符串回复
#### 常用配置
* daemonize 是否以守护进程的形式运行
* port 监听的端口
* logfile Redis日志
* dir 工作目录

### Redis高性能的原因
* 纯内存
* 非阻塞IO， 自己实现的事件模型
* 单线程，可以避免线程的切换，创建和销毁

注意事项：
* 一次只执行一条命令
* 拒绝长命令，复杂度高的命令
* 在打开资源文件时，会开启新的线程
