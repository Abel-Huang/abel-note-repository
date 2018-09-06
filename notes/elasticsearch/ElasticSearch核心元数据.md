### ElasticSearch核心元数据
#### _index 元数据
1. 代表一个document放在哪个index中
2. 类似的数据放在一个索引中，每个fields基本是相同的
3. 名称必须小写，不能以下划线开头

#### _type 元数据
在es6中只能有一个，在后续的版本中将被移除

#### _id 元数据
1. 代表document的唯一标识
2. 可以手动指定，也可以不指定，由es自动生成id

#### _id元数据的两种生成方式
1. 手动
* 手动指定document id
* `put  /index_name/type_name/id`
* 使用其他数据导入的时候，如果数据库，可以以数据库的主键

2. 自动生成
* 系统自动生成的20位的id，不会出现重复
* `post  /index_name/type_name`
* 采用GUID算法，可以保证在分布式环境下，不同节点同一时间创建的id是不冲突的

#### _source元数据
* _source是查询的结果，默认情况下是将数据全部返回回来
* 可以通过指定参数只获取部分字段数据，`GET /test_index/test_type/1?_source=test_filed1,test_filed2`，多个字段用逗号隔开

#### document
* 全量替换
`PUT  /index_name/type_name/id`
如果id存在就会重新生成一个新的文档，替换原来的旧的文档
* 强制创建
`PUT  /index_name/type_name/id/_create`
如果这个id存在就会报错，如果不存在就会重新生成一个新的文档






