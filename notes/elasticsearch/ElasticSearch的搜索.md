### ElasticSearch的搜索

#### 搜索返回的内容
* took 花费时间
* hits
    * total 搜索的结果条数
    * max_score搜索结果中最大相关度分数
    * hits 搜索到的数据 ，默认是前十条，按照score降序排列
* timeout机制，指定每个shard就只能在timeout的时间范围内将搜索的部分数据(也可能会全部搜索到) 直接返回给client，不会等到所有数据都搜索完，提供了更好的用户体验。默认情况下是没有timeout的。

#### 搜索问题
##### 分页
```
GET  /_search?from=${num1}&size=${num2}
```
通过from和size进行分页，其中from是开始的数据条数，取值从0开始，size表示分页的每页的大小。
##### deeping paging
deep paging简单来说，就是搜索的特别深，比如总共有60000条数据，每个shard上分20000条，每页是10条数据，这个时候你要搜索到第1000页，实际上要拿到的是10001-10010，该怎么拿呢？ 
请求首先可能是打到一个不包含这个index的shard的node上，这个node就是一个coordinate node，这个coordinate node就会将搜索请求转发到index的三个shard所在的node上去。

要搜索60000条数据中的第1000页，实际上每个shard都要将内部的20000条数据中的第10001-10010条数据拿出来，不是10条，是10010条数据，3个shard每个shard都返回10010条数据给coordinate node，coordinate node会收到总共30030条数据，然后取到排位最高的前10条数据，其实就是我们要的最后的第1000页的10条数据。

**弊端**

搜索过深的时候就需要在coordinate node上保存大量的数据，还要进行大量数据的排序，排序之后再取出对应的那一页，所以这个过程，既消耗网络宽带，耗费内存，还消耗cpu。这就是deep paging的性能问题，我们应该尽量避免出现这种deep paging操作。

##### query string
query string就是在http请求将搜索参数附加到URL的请求参数上。
* 表示搜索中带有这个字段field和这个值value的内容
```
GET /index/type/_search?q=field:value
```
有+和没+含义相同
```
GET /index/type/_search?q=+field:value
```
* 表示搜索中不带有这个字段和这个值的内容
```
GET /index/type/_search?q=-field:value
```
* _all metadata
只要包含有value这个字段就可以被索引到
```
GET /index/type/_search?q=value
```
***原理**
使用这个命令时会将一个document中所有的字段field以字符串的形式串联起来，变成一个长字符串，作为_all字段的值，然后对这个字段建立索引，没有指定field时就会默认采用这种形式。
不建议在生产环境使用
