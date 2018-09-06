## Mybatis缓存
Mybatis是支持缓存的，拥有二级缓存，默认情况下只开启一级缓存。
* 一级缓存是Session会话级别的缓存，位于表示一次数据库会话的SqlSession对象之中，又被称之为本地缓存。
一级缓存是MyBatis内部实现的一个特性，用户不能配置，默认情况下自动支持的缓存，用户没有定制它的权利
（不过这也不是绝对的，可以通过开发插件对它进行修改）；
* 二级缓存是Application应用级别的缓存，它的是生命周期很长，跟Application的声明周期一样，
也就是说它的作用范围是整个Application应用。

### Mybatis一级缓存
Mybatis的一级缓存是SQLSession级别的缓存，是一个粗粒度的缓存。

#### 一级缓存的生命周期
1. MyBatis在开启一个数据库会话时，会创建一个新的SqlSession对象，SqlSession对象中会有
一个新的Executor对象。Executor对象中持有一个新的PerpetualCache对象；当会话结束时，
SqlSession对象及其内部的Executor对象还有PerpetualCache对象也一并释放掉。
2. 如果SqlSession调用了close()方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用。
3. 如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用。
4. SqlSession中执行了任何一个update操作(update()、delete()、insert()) ，都会清空
PerpetualCache对象的数据，但是该对象可以继续使用

#### 一级缓存的工作流程
1. 对于某个查询，根据statementId,params,rowBounds来构建一个key值，根据这个key值去缓存Cache中
取出对应的key值存储的缓存结果；
2. 判断从Cache中根据特定的key值取的数据数据是否为空，即是否命中；
3.  如果命中，则直接将缓存结果返回
4.  如果没命中：    
    4.1  去数据库中查询数据，得到查询结果   
    4.2  将key和查询到的结果分别作为key,value对存储到Cache中；      
    4.3 将查询结果返回；   
5. 结束。

#### 一级缓存的实现原理
PerpetualCache实现原理其实很简单，其内部就是通过一个简单的HashMap<k,v> 来实现
mybatis认为，对于两次查询，如果以下条件都完全一样，那么就认为它们是完全相同的查询。
1. 传入的statementId
2. 查询时要求的结果集中的结果范围
3. 这次查询所产生的最终要传递给JDBC java.sql.Preparedstatement的Sql语句字符串（boundSql.getSql() ）
4. 传递给java.sql.Statement要设置的参数值
即：  MyBatis认为的完全相同的查询，不是指使用sqlSession查询时传递给算起来Session的所有参数值
完完全全相同，你只要保证statementId，rowBounds,最后生成的SQL语句，以及这个SQL语句所需要的参数
完全一致就可以了。

#### 一级缓存的总结
1. Mybatis会话(Session)级别的缓存，简单地使用HashMap来维护，并没有对HashMap的容量和大小进行限制。
2. 一级缓存是一个粗粒度的缓存，没有更新缓存和缓存过期的概念。

###  Mybatis二级缓存
MyBatis的二级缓存是Application级别的缓存(会跨Session进行缓存, 可以认为缓存是SqlSessionFactory级别的缓存)，
它可以提高对数据库查询的效率，以提高应用的性能
* 映射语句文件中的所有select语句将会被缓存。
* 映射语句文件中的所欲insert、update和delete语句会刷新缓存。
* 缓存会使用默认的Least Recently Used（LRU，最近最少使用的）算法来收回。
* 根据时间表，比如No Flush Interval,（CNFI没有刷新间隔），缓存不会以任何时间顺序来刷新。
* 缓存会存储列表集合或对象(无论查询方法返回什么)的1024个引用
* 缓存会被视为是read/write(可读/可写)的缓存，意味着对象检索不是共享的，而且可以安全的被调用者修改，
不干扰其他调用者或线程所做的潜在修改。
### 二级缓存的划分
MyBatis并不是简单地对整个Application就只有一个Cache缓存对象，它将缓存划分的更细，即是Mapper级别的，
即每一个Mapper都可以拥有一个Cache对象，具体如下：
1. 为每一个Mapper分配一个Cache缓存对象（使用<cache>节点配置）；
2. 多个Mapper共用一个Cache缓存对象（使用<cache-ref>节点配置）。

### 二级缓存的开启条件
1.  MyBatis支持二级缓存的总开关：全局配置变量参数  `cacheEnabled=true`
2. 该select语句所在的Mapper，配置了<cache> 或<cached-ref>节点，并且有效
3. 该select语句的参数 useCache=true

### 缓存使用顺序
 二级缓存 ---> 一级缓存---> 数据库
