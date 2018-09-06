## Spring Ioc

### Spring Ioc容器的基础
Spring Ioc的容器的设计主要是基于BeanFactory和ApplicationContext两个接口，其中ApplicationContext是BeanFactory的子接口之一。
大部分情况下都会以ApplicationContext作为SpringIoc的容器。

#### Spring Ioc容器的初始化和依赖注入
Bean的初始化和依赖注入在SpringIoc容器中是两大步骤，在初始化之后，才会进行依赖注入。
Bean的初始化分为3步：
(1). Resource定位，这一步是Spring Ioc容器根据开发者的配置，进行资源的定位，在Spring的开发中，可以通过XM和注解的方式进行；
(2). BeanDefinition的载入，这个过程就是Spring根据开发者的配置获取对应的POJO用以生成对应的实例的过程。
(3). BeanDefinition的注册，这个过程就相当于之前通过BeanDefinition载入的POJO往SpringIoc容器中注册，这时就可以得到Bean。
默认情况下只有通过使用Spring Ioc容器的getBean()方法获取时，Bean才会完成初始化，从而完成依赖注入。

#### Spring Bean的生命周期
Spring Bean的创建和Bean的销毁都是由Spring Ioc容器管理的，这样一整个流程就是Spring Bean的生命周期。


### 装配Bean的方式
Spring装配Bean有三种方式进行配置：
* 在XML中显式配置
* 在Java接口和类中实现配置
* 隐式Bean的发现机制和自动装配

#### Bean的装配方式的选择
1. 基于约定优于配置的原则，最优先的应该是通过隐式Bean的发现机制和自动装配原则。
2. 无法使用自动装配的情况下优先考虑使用Java接口和类中实现配置，可以避免XML配置的泛滥。
3. 在上述方法都无法使用时才考虑选择使用XML去配置Spring Ioc容器。

#### 依赖注入的三种方法
从注入方法来看，Ioc可以分为三种类型：构造函数注入、Setter注入和接口注入。
Spring主要是构造函数注入和Setter注入。
* 构造器注入： 依赖于构造方法实现
* setter注入：最主流的方式，利用了Bean定义的setter方法来完成注入，灵活且可读性高，基于反射实现的。
* 接口注入： 有时资源并非来自系统，而是来自外界，如数据库连接资源，可以用接口注入的方式获取它。

#### 基于XML的配置
使用XML装配Bean需要定义对应的XML(XSD)文件，会定义Bean的一些元素，
* id属性 Spring找到这个Bean的编号，非必须，如果没有声明id， Spring会采用全限定名#{numberl}的格式生成编号
* class是一个类的全限定名
* property元素定义类的属性，其中name定义的是属性名称，value是其值。
* List、Map、Set等复杂属性
* 还可以通过命名空间进行装配。

#### 通过注解装配Bean
Spring提供了两种方式让Spring Ioc容器发现Bean
* 组件扫描：通过定义资源的方式，让Spring Ioc容器扫描对应的包，从而装配Bean
* 自动装配：通过注解定义，使得一些依赖关系可以通过注解完成

1. 使用@Component装配Bean
使用Component代表Spring Ioc会把这类扫描成Bean实例，value属性表示Bean的id。
@Value表示值的注入
使用@ComponentScan进行包扫描，默认扫描当前路径

2. 自动装配@Autowired
推荐使用

3. 自动装配的歧义性
@Primary
当Spring Ioc通过一个接口或者抽象类进行注入时，如果有多个实现类，可以通过@Primary避免混淆
@Qualifier
默认情况下自动装配是根据类型进行装配的，@Qualifier采用名称进行查找，可以避免歧义

3. 使用@Bean装配
@Component只能用在类上，@Bean可以注解到方法上，并且将返回的对象作为Spring的Bean存放在Ioc容器中

#### 基于Java的显式配置
通过@Configuration和@Bean搭配来完成


### Spring Bean的作用域
* 单例，singleton， 默认选项，在整个应用中，Spring只生成一个Bean实例
* 原型， prototype，每次注入或者获取Bean时，Spring Ioc都会为它创建一个新的实例
* 会话， session，在Web中使用，每次会话，Spring Ioc都会为它创建一个新的实例
* 请求， request，在Web中使用，每次请求Spring Ioc都会为它创建一个新的实例， 但是不同请求会创建不同的实例














