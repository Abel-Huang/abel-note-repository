## Mybatis原理-1
Mybatis的核心组件
### Mybatis的核心组件
* SqlSessionFactoryBuilder(构造器)：它会根据配置或者代码来生成SqlSessionFactory，采用的是Builder模式
* SqlSessionFactory(工厂接口):用于生成SqlSession，使用工厂模式
* SqlSession(会话)：表示一次SQL执行的会话。(开发中一般不会使用，而是使用Mapper接口代替, 换种表达方式就是
MyBatis和数据库的交互有两种方式：使用传统的MyBatis提供的API即SQLSession；使用Mapper接口)
* SQL Mapper（映射器）：Mybatis的新组件(相对于SqlSession来说)，由一个Java接口和XML文件(或注解)构成。
需要给出对应的SQL和映射规则，负责发送SQL去执行，并返回结果。

### SqlSessionFactory
使用Mybatis首先是使用配置或者代码去生产SqlSessionFactory，通过SqlSessionFactoryBuilder的Builder模式使用
Configuration作为引导构建SqlSessionFactory。   
每个基于Mybatis的应用都是以一个SqlSessionFactory的实例为中心的，而SqlSessionFactory的唯一作用就是生成
Mybatis的核心接口对象SqlSession。通常使用单例模式，配置方法有配置文件和Java代码两种形式。
#### XML构建
配置Mybatis的配置文件，一般是mybatis-config.xml，通过SqlSessionFactoryBuilder的builder()方法去创建
SqlSessionFactory。采用XML创建的形式，所有信息都在配置文件中，便于以后维护和修改。
#### Java代码创建
使用Java代码进行配置并生成Configuration, 实际步骤与XML相同，只是需要调用Java方法实现。

### SqlSession
SqlSession是Mybatis的核心接口，作用类似于一个JDBC的Connection对象，代表着一个廉洁资源的启用，具体作用
有三个：
* 获取Mapper接口
* 发送SQL给数据库
* 控制数据库事务
SqlSession是一个门面接口，真正执行SQL的是Executor方法。

### Mapper--映射器
Mapper是Mybatis最重要、最复杂的组件，可以配置以下内容：
* 描述映射规则
* 提供SQL语句，并可以配置SQL参数类型、返回类型、缓存刷新等信息
* 配置缓存
* 提供动态SQL
映射器的主要作用就是将SQL查询到的结果映射为一个POJO，或者将POJO的数据插入到数据库中，
并定义一些缓存等重要内容。

#### 使用XML实现映射器
主要分为两个部分：接口和XML文件。

#### 使用注解实现映射器
可以直接在接口中使用注解，可以不用XML文件。如果SQL语句比较复杂，直接在注解中会使得源代码非常
复杂，可读性和维护性变差，一般用在简单的SQL语句中。

#### 发送SQL语句的两种方式
1. SqlSession发送SQL
2. Mapper接口发送SQL语句
3. 两种方式的对比：
    * Mapper接口可以消除SQLSession的模板代码
    * 方便利用IDE进行代码的错误提示和校验
 使用Mapper接口已经成为主流，Spring集成后就是使用 Mapper接口的形式。
 
### 生命周期
生命周期就是每一个对象应该存活的时间，比如一些对象使用完后就该被JVM销毁。

#### SqlSessionFactoryBuilder
SqlSessionFactoryBuilder作用在于创建SqlSessionFactory， 创建成功后SqlSessionFactoryBuilder
就失去了作用，不需要长期存活。

#### SqlSessionFactory
SqlSessionFactory可以认为是一个数据库连接池，作用是创建SqlSession接口对象。因为Mybatis本质是对数据库
的操作， 所以SqlSessionFactory的生命周期存在于整个Mybatis的应用中，所以一旦创建了SqlSessionFactory，
就需要长期保存，可以认为SqlSessionFactory的生命周期等同于Mybatis的生命周期。  
一般应用中SqlSessionFactory最好是是一个单例。

#### SqlSession
SqlSession相当于一个数据会话连接，应该存活在一个业务请求中，处理完请求后就应该关闭连接，归还给
SqlSessionFactory，如果无限使用会导致数据库连接资源耗尽，系统崩溃。

#### Mapper
Mapper是一个接口，由SqlSession建立，所以最好与SqlSession保持一致。
