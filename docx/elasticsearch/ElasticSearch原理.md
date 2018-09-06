## ElasticSearch原理
### 路由分片算法
` shard = hash(routing) % number_of_primary_shards`
routing 值是一个任意字符串，它默认是 _id 但也可以自定义
number_of_primary_shards是主分片的数量

安装
启动
插件

### ES和RDB的一个对比
在Elasticsearch中，文档归属于一种类型(type),而这些类型存在于索引(index)中，我们可以画一些简单的对比图来类比传统关系型数据库：

Relational DB -> Databases -> Tables -> Rows -> Columns
Elasticsearch -> Indices   -> Types  -> Documents -> Fields
6.x版本的ES同一个索引中只支持一个type，而在7.x之后将会完全移除type。


## 基本API
### 索引
#### 新建索引
PUT /{index}/{type}/{id}
{
  "field": "value",
  ...
}

### 搜索
#### 空搜索
`GET /_search` 
没有指定任何查询条件，只返回索引中的所有文档

#### 多索引搜索
`/gb,us/_search` 在index gb和us中搜索
`/gb,us/user,tweet/_search` 在index gb和us中搜索类型user和tweet

#### 分页
`GET /_search?size=5&from=5` 使用size和from进行分页

#### 简易搜索
将搜索条件在查询字符串中，如：
`GET /_all/tweet/_search?q=tweet:elasticsearch` 搜索所有索引中类型为tweet索引中tweet中包含elasticsearch的文档。

### 全文搜索
倒排索引
先建立索引，然后进行搜索
### Lucene
ElasticSearch就是基于Lucene开发的。

### ElasticSearch
Node 节点
分片
备份

#### 提供了Lucene不支持的特性
1. 自动维护数据的分布式到多个节点的索引的建立和请求，分布式，可扩展
2. 自动维护数据的冗余副本，高可用
3. 封装了更多的高级功能，地理位置，高级搜索聚合

#### 特点：
分布式，搜索，数据分析
* 分布式的搜索引擎和数据分析引擎
* 全文搜素，结构化搜索，数据分析
* 对海量数据进行近实时处理

### ElasticSearch分布式原理
#### ElasticSearch对分布式机制的隐藏
集群发现机制 cluster discovery，节点通过cluster name加入集群
shard负载均衡，es会自动保持每个节点的均衡的读写负载均衡
shard副本，请求路由，扩容，shard重分配
均对用户隐藏
#### 扩容
* 垂直扩容：更换更强的硬件设备
* 水平扩容：增加新的机器
* 扩容对应用程序透明
#### rebalance
有些服务器的负载会重一些
#### master节点
* 管理es集群的元数据
* 默认情况下，会自动选择一台节点，选主
* master节点不承载所有的请求，所以不会存在单点瓶颈
#### 节点平等的分布式架构
* 节点对等，每个节点都可以收到所有的请求
* 自动请求路由
* 响应收集

### primary shard和replica shard的机制
* 一个index会包含一个或多个shard
* 每一个shard都是一个Lucene实例，有完整的建立索引和处理请求的能力
* 增减shard时会自动进行负载均衡
* 同一个document只能存在与一个shard中，不会存在于多个shard中
* 创建索引时primary 就会固定，但是replica是可以根据节点进行负载均衡的
* replica是primary的副本，负责容错和担任请求负载
* primary默认是5， replica默认是1(这个1是指每个primary都有1个replica，所以5个primary有5个replica)
* 同一个primary和replica不能再同一个节点上, 一个节点上可以分配多个shard


#### 新建索引设立分片数量
```
PUT /test_index
{
    "settings:{
        "number_of_shards":3,
        "number_of_replicas":1
    }
}
```
#### 两个Node下replica和primary的分配
* 在单节点集群中加入新的节点，原来节点会将replica分配到新的节点，并且会将数据复制到新的节点中
* primary和replica都可以承担读请求，但是只有primary才能承担写请求

#### 扩容方式
* 扩容后，每个shard可以占有更多的资源，性能就会更好
* 对于系统的扩容瓶颈，可以增加replica的数量，因为primary的数量是固定的，通过增加replica的数量提供集群的吞吐量
* 提高容错性，在尽可能多的机器宕机的情况下依然可以保证数据不丢失



