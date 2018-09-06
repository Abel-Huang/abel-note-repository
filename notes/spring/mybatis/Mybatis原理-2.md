## Mybatis原理-2
Mybatis的解析和运行原理

### SqlSessionFactory的构建过程
主要分为两步：
第一步：通过XMLConfigBuilder构造Configuration对象
第二步：使用Configuration对象创建SqlSessionFactory对象
#### 构建Configuration
在创建SqlSessionFactory的过程中，Configuration是最重要的，起作用是：
* 读入配置文件，包括基础的XML和映射器等
* 初始化一些基础配置
* 提供单例，为后续擦混构建SessionFactory服务，提供配置的参数
* 执行一些重要对象的初始化方法
Configuration是通过XMLConfigBuilder构建的，首先会读出所有的XML配置的
信息，然后把他们解析并保存在Configuration单例对象中。初始化对象：
* properties 全局参数
* typeAliases 别名
* Plugins 插件
* objectFactory 对象工厂
* objectFactory 对象包装工厂
* reflectFactory 反射工厂
* settings 环境设置
* environments 数据库环境
* databaseIdProvider 数据库标识
* typeHandlers 类型转换器
* Mappers 映射器

#### 构建映射器的内部组成
一般而言MyBatis中的一条SQL和它相关的配置信息是由三个部分组成的，分别是MappedStatement、
SqlSource和BoundSql。
* MappedStatement：作用是保存一个映射器节点(select|update|insert|delete)的内容。包括配置的
SQL、SQl的id、缓存、resultType， resultMap等配置信息。包括一个重要属性SqlSource，Mybatis可以
通过它来获得某条SQL配置的所有信息。

* SqlSource： 是提供BoundSql的地方，是MappedStatement的一个属性，可以根据上下文和解析参数
得到所需要的SQL即BoundSql对象。

* BoundSql：是一个结果对象，是SqlSource通过对SQL和参数的联合解析得到的SQL和参数，是建立SQL和参数的
地方。主要属性是： sql， parameterObject， parameterMappings.

#### 构建SqlSessionFactory
`SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream)`
 Mybatis会根据文件流生成Configuration对象，进而构建SqlSessionFactory。
 
### SqlSession的运行过程
SqlSession主要用到了反射和动态代理，特别是JDK的动态代理。

#### 映射器的动态代理
Mapper作为一个动态代理对象的接口，Mapper的XML文件的命名空间对应的是这个接口的全限定名，而方法就是那条
SQL的id，这样Mybatis就可以根据全路径和方法名，将其和代理对象绑定起来。

#### SqlSession的四大对象
1. Executor:代表执行器，由其调度StatementHandler、ParameterHandler、ResultSetHandler等来执行
对应的SQL。其中Statement是最重要的。
    * Executor是于数据库交互的对象，提供了查询、更新和相关的事务方法
2. StatementHandler的作用是使用数据库的Statement(PreparedStatement)执行操作，是四大对象的核心，
起到承上启下的作用
    * 数据库会话器，就是专门用来处理数据库会话的。
3. ParameterHandler使用来处理SQL参数的
    * MyBatis通过ParameterHandler对于编译语句进行参数设置。
4.ParameterHandler是进行数据集(ResultSet)的封装返回处理的。
一条SQL语句的执行过程：
Executor先调用ParameterHandler的prepare()方法预编译SQL，同时设置一些基本运行的参数，然后使用parameterize()
方法启用ParameterHandler设置参数，完成预编译，执行查询，update()也是这样。如果是查询，MyBatis会使用
ParameterHandler封装结果返回给调用者。