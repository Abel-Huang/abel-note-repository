## ElasticSearch的基本API

### 索引
#### 创建空索引
```
put  /index_name
{
    "settings" : {
        "number_of_shards" : 3,
        "number_of_replicas" : 1
    }
}
```
创建一个索引名为index_name的空索引，并且设置3个主分片，一个备份分片。

#### 获取索引
```
GET /index_name
```
获取index_name的索引信息，支持通配符搜索

#### 删除索引
```
DELETE /index_name
```
删除名称为index_name的索引。
```
DELETE /index_name1, index_name2
```
删除多个索引
```
DELETE /_all
```
删除所有的索引。

#### 修改索引设置
```
PUT /index_name/_setting
{
    "number_of_replicas": 1
}
```
修改index_name的索引，设置备份分片为1

#### 打开或关闭索引
```
POST /index_name/_close
{
}
```
关闭索引，搜索时只能显示元数据不能显示具体的数据
```
POST /index_name/_open
{
}
```
开启索引，可以搜索的具体的数据

#### 索引别名
POST /
{
  "actions" : [
          { "remove" : { "index" : "index_name", "alias" : "alias1" } },
          { "add" : { "index" : "index_name", "alias" : "alias2" } }
      ]
}
remove用于删除别名，add用于添加别名，可以先使用remove然后使用add用于修改指定索引的别名。

### 文档
#### 插入数据
```
PUT  /index_name/type_name/doc_id
{
     "key1": "value1",
     "key2": "value2",
     "key3": "value3"
     ......
}
```
指定文档id进行上传
```
POST  /index_name/type_name/
{
     "key1": "value1",
     "key2": "value2",
     "key3": "value3"
     ......
}
```
不指定文档id上传，系统会生成一个唯一的文档id

#### 修改文档
文档在ElasticSearch中是不可变，修改文档的逻辑是新建一个文档覆盖旧文档。
```
PUT  /index_name/type_name/doc_id
{
     "key11": "value11",
     "key22": "value22",
     "key33": "value33"
}
```
响应中_version字段会加一。

#### 删除文档
```
DELETE  /index_name/type_name/doc_id
```
删除指定id的文档


### 查询
#### 空搜索
```
GET /_search
```
没有指定任何查询条件，只返回索引中的所有文档

#### 多索引搜索
```
GET /index_1,index_2/_search
``` 
在index index_1和index_2中搜索

```
GET /index_name/type_name/doc_id/_source
```
只获取指定id的source内容，会忽略元信息
```
GET /index_1,index_2/type_1,type_2/_search
```
 在indexindex_1和index_2中搜索类型type_1和type_2

```
POST  /_mget
{
  "docs" : [
            {"_index" : "index_name1","_type" : "type_name1","_id" : "doc_id1"},
            {"_index" : "index_name2","_type":"type_name2","_id" :"doc_id2"}
      ]
}
```
#### 分页
```
GET  /_search?size=5&from=5
``` 
使用size和from进行分页

#### 简易搜索
将搜索条件在查询字符串中，如：
```
GET /_all/index_name/_search?q=filed_name:field_value
``` 
搜索所有索引中类型为index_name索引中filed_name中包含filed_value的文档。











