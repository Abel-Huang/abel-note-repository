## ElasticSearch基本原理
### Lucene和ElasticSearch
ElasticSearch基于Lucene，隐藏复杂性，提供了简单易用的restful API接口，和其他语言的接口。

### 核心概念
#### Near Realtime 近实时
秒级延迟
* 写入到能被搜索有秒级延时
* 聚合搜索需要秒级时间
#### Cluster 集群
一个节点有多个节点，通过集群的名称加入集群，默认是"elasticsearch"
#### Node 节点
集群中的一个节点
#### Document&Field 文档和字段，
文档是es中最小的数据单元
#### Index 索引
包含一堆有相似结构的文档数据结构
#### type 类型
v6中一个index中只能有一个type，v7中将会彻底移除
#### shard 分片
index会被拆成多个shard，每个shard会存放部分数据，这些shard会存放在多台服务器上。
好处：
* 横向扩展
* 并行分布式，提升吞吐量和性能
#### replica 备份，副本
shard是primary shard，简称为shard
replica是replica shard，简称为replica
好处：
* 高可用性
* 提升搜索这类请求的吞吐量和性能
默认情况下一个index是由5个primary shard和5个replica shard。

### 安装和使用
#### 安装
#### 基本包结构
#### kibana的安装使用
使用kibana作为es的基本操作入口
http://localhost:5601   在Dev Tools中进行操作 

### 使用入门
面向文档，以json作为存储格式

#### 简单的集群管理
(1). 快速检查集群健康状况
`GET /_cat/health?v`
查看status参数，green表示primary和replica都是active状态的，处于不可用状态
yellow表示所有的primary是active，但是部分replica不是active，
red表示不是所有primary和replica都是active状态的
注意：primary和replica不能分配在同一个节点下面
(2). 查看集群中的索引
`GET /_cat/indices?v`
(3). 简单的索引操作
创建索引：
`PUT /index_name?pretty`
删除索引：
`DELETE /index_name?pretty`
(4). 文档的CURD操作
(1).新增
```
PUT index_name/type_name/id`
{
    body
}
```
(2).查询
`GET index_name/type_name/id`
(3). 更新
替换修改，需要带上所有的请求信息
```
PUT index_name/type_name/id`
{
    body
}
```
修改更新，只需要带上修改的field,不需要带上整个的请求体
```
POST index_name/type_name/id/_update
{
    "doc": {
       "field_name" : "field_value"
    }    
}
```
(5). 删除文档
```
DELETE index_name/type_name/id
```














