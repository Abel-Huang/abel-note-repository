### ElasticSearch高并发下的问题

#### ES的并发冲突问题
当多个线程同时操作ES里面的数据时可能会存在数据已经被修改，但是仍然可能会读到旧的数据，这个就是并发冲突问题。

#### 悲观锁和乐观锁并发控制方案
* 悲观锁
悲观锁常见于关系型数据库的事务控制。
悲观锁是在各种情况下都会进行上锁，上锁之后只有一个线程可以操作。
 悲观锁的优点
 * 方便，对程序来说简单，不需要额外操作
 * 并发能力低

* 乐观锁
ES采用的是乐观锁的形式。
乐观锁是不加锁的，每个线程是可以任意操作。
ES的乐观锁通常会引入版本号，通过版本号来确定内容是否修改，以便决定是否需要将这次的数据写会。
    * 并发能力高，不需要加锁
    * 每次更新需要对比版本号，可能需要重新加载数据

#### ES的乐观锁机制
(1). _version元数据
第一次创建document时，_version被置为1，以后每次修改都会被加一，删除也会增加一。
(2). es内部的多线程异步并发修改时，多个请求之间是没有一定的顺序的，是基于自己的_version版本号进行乐观锁并发控制的。
(3). 使用ES内部的_version
```
PUT  /index_name/type_name/id_value?version=current_version
{
}
```
在修改数据时请求带上version字段，只有version的值与当前文档中的值一致，才会进行修改，否则就会出现版本冲突错误，并以此来实现乐观锁的并发控制。
(4). 也可以使用外部的自己维护的version进行控制
```
PUT  /index_name/type_name/id_value?version=current_version&version_type=external
{
}
```
当设置`version_type=external`时，我们需要自己手动维护version的值，并且在进行修改时version的值必须要大于当前文档中的version的值才能进行修改。

(5). partial update 局部更新
**全量更新** ，将整个文档删除重新生成新的文档，基本流程为：
* 用户将需要修改的全量数据封装好
* 发送一个PUT请求到es中，进行全量替换
* es将旧的document标记为deleted，然后创建一个新的document，值就是之前上传的内容
语法为：
```
PUT /index/type/id
{
    ...data
}
```
**部分更新**  只需要修改部分的field，不需要修改整个全量数据，基本流程为：
* 将在内部将传过来的field更新到document的json中
* 将旧的document标记为deleted
* 将修改后的document创建出来

语法为
```
POST /index/type/id/_update
{
    "doc":{
       ... 修改部分数据
    }
}
```
**优点**
* 所有的查询修改和写回操作都发生es的一个shard内部，避免了网络数据传输的开销
* 减小了查询和修改的时间间隔，可以有效减少并发冲突的情况

(5). partial update 的乐观锁并发控制
```
POST /index/type/id/_update?retry_on_conflict=N&version=M
{
    
  "doc":{
       ... 修改部分数据
    }
}
```
partial update 可以通过retry_on_conflict设置失败重试次数
关于retry策略：
* 更新时会使用version作为标准
* 在更新失败后会获取document的数据和最新版本号
* 使用最新版本号进行更新
* 如果成功则更新完成，如果失败则重复上述两个步骤，直到达到retry的最大次数

#### 批量查询mget
**好处**
批量查询可以减少网络请求次数，一次请求将所有的结果返回出出来,在大量查询时推荐使用这种方式可以提升数倍的性能
```
GET  /_mget
{
  "docs":[
    {
      "_index":"shop",
      "_type":"product",
      "_id":8
    },
    {
      "_index":"shop",
      "_type":"product",
      "_id":9
    }]
}
```
如果是在同一个index和type下面可以简写为：
```
GET  /shop/product/_mget
{
    "ids":[1,2] 
}
```
#### bulk操作
1. bulk一次可以提交多个不同类型的请求和执行
2.每一个操作需要两个json串，delete操作只需要1个json串就可以了
3. 支持bulk类型操作的语法为
* delete: 删除一个文档
* create： `PUT /index/type/id/_create` 强制创建
* inde： 普通的put操作，可以是创建文档，也可以是全量替换文档
* update：执行普通的partial update操作
4. bulk api对json有严格的要求，每个json内部不能换行，只能放一行，json串之间必须有一个换行
否则报错如下：
```
{
  "error": {
    "root_cause": [
      {
        "type": "json_e_o_f_exception",
        "reason": "Unexpected end-of-input: expected close marker for Object (start marker at [Source: org.elasticsearch.transport.netty4.ByteBufStreamInput@488f81fb; line: 1, column: 1])\n at [Source: org.elasticsearch.transport.netty4.ByteBufStreamInput@488f81fb; line: 2, column: 3]"
      }
    ],
    "type": "json_e_o_f_exception",
    "reason": "Unexpected end-of-input: expected close marker for Object (start marker at [Source: org.elasticsearch.transport.netty4.ByteBufStreamInput@488f81fb; line: 1, column: 1])\n at [Source: org.elasticsearch.transport.netty4.ByteBufStreamInput@488f81fb; line: 2, column: 3]"
  },
  "status": 500
}
```
处理命令可以参考为：
```
POST /_bulk
{"delete":{"_index":"shop","_type":"product","_id":10}} # 删除
{"create":{"_index":"shop","_type":"product","_id":5}}    # 新增
{"name":"super yagao","desc":"very nice ","price":80,"producer":"super producer","tags":["good","popular"]}
{"index":{"_index":"shop","_type":"product","_id":6}} # 索引
{"test_filed":"test_index"}
{"update":{"_index":"shop","_type":"product","_id":2}} # 修改
{"doc":{"name":"super xiyifen","desc":"very very nice ","price":50,"producer":"super producer","tags":["good","popular"]}}
```
5. 如果同时执行多个处理命令，任意一个命令失败是不会影响其他命令的，但是在返回的结果中会给出这个失败的错误日志。
6. bulk request会加载到内存中，太大会导致性能下降，一般是1000-5000条或者内存大小在5-15M之间。

#### document的路由原理
1. document路由到shard上
每一个document都会路由到一个shard上，即每一个文档都会被分配到一个shard上，无论是增删改查都是通过路由算法找到文档被索引的shard。
2. 路由算法
`shard = hash(routing) % number_of_primary_shards` 
routing：每次在增删改查一个document时，都会产生一个routing number，默认是document的id(可以手动指定)
routing的值是决定路由的最重要的因素，同一个routing产生的hash值一定是相同的，并且算法最终的结果是在0-number_of_primary_shard-1之间。
3. 默认的routing值是id
也可以通过手动指定这个值，这样可以保证可以路由到一个确定的shard上。
4. 在创建索引后，primary shard的数量不可修改，因为修改primary的数量会导致无法索引到正确的shard，进而导致数据丢失。

#### document增删改的内部原理
1. 客户端选择一个node发送请求，这个node就是coordinating node(协调节点)
2. coordinating node对document进行路由处理，将数据发送到存在这个document的primary所在的node上
3. primary进行处理，处理后将数据同步到replica上
4. 然后coordinating node会将从primary更新的内容响应给客户端

#### document查找的内部原理
与之前的修改机制不同之处在于，coordinating node对document进行路由处理可以将请求分发到仍以一个含有这个document的shard，primary和replica都是可以执行读操作的。
分发过程会采用round-robin随机轮询算法均匀将请求分发给所有有该文档的shard，以达到负载均衡的效果。
**特殊情况**：
可能存在这种情况，只有primary shard上存在，此时replica上没有复制数据，若此时请求到这个replica，会出现找不到这个文档的情况，当建立索引这个过程完成后，primary和replica上都会有数据了。

#### ES写一致性原理(有点难度)
1. ES在写入时可以通过consistency参数指定写一致性的参数，如
```
put  /index/type/id?consistency=quorum
```
参数可以取值为：
* one：至少有一个有一个primary是active的，就可以执行写操作
* all： 所有的primary和replica都是活跃的，才可以执行写操作
* quorum：大部分的shard是活跃的，才可以执行写入操作
2. quorum的大部分具体取值为：
` quorum = int ((primary + number_of_replicas)/2) + 1`
3. 只有在number_of_number>1时，quorum才能生效

#### bulk api需要规定json格式的原因
1. bulk格式请求的json数据请求必须按照下面的格式
```
{"action" : {"meta}}
{"data}
```
2. 如果是严格的json格式，es拿到的json后，可以方便的按行进行切割然后解析成单独的请求分发给不同的shard进行处理。
3. 如果是标准的json格式，es拿到json解析为一个JSONArray对象，造成内存中有非常大的临时对象，不仅耗费大量内存空间，而且会对Jvm GC造成巨大的负担。













