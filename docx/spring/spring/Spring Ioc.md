# Spring Ioc

## Spring Ioc容器的基础
Spring Ioc的容器的设计主要是基于BeanFactory和ApplicationContext两个接口，其中ApplicationContext是BeanFactory的子接口之一。
大部分情况下都会以ApplicationContext作为SpringIoc的容器。

### Spring Ioc容器的初始化和依赖注入
Bean的初始化和依赖注入在SpringIoc容器中是两大步骤，在初始化之后，才会进行依赖注入。
Bean的初始化分为3步：
(1). Resource定位，这一步是Spring Ioc容器根据开发者的配置，进行资源的定位，在Spring的开发中，可以通过XM和注解的方式进行；
(2). BeanDefinition的载入，这个过程就是Spring根据开发者的配置获取对应的POJO用以生成对应的实例的过程。
(3). BeanDefinition的注册，这个过程就相当于之前通过BeanDefinition载入的POJO往SpringIoc容器中注册，这时就可以得到Bean。
默认情况下只有通过使用Spring Ioc容器的getBean()方法获取时，Bean才会完成初始化，从而完成依赖注入。

### Spring Bean的生命周期
Spring Bean的创建和Bean的销毁都是由Spring Ioc容器管理的，这样一整个流程就是Spring Bean的生命周期。
1. 读取 Bean 的配置信息;
    * xml 配置文件
    * Java 类@Configuration
    * 注解 @Autowired
2. 根据 Bean 注册表实例化 Bean(BeanFactory 中的 BeanDefinition map);
3. 将 Bean 实例放在 Spring 容器(BeanFactory 或 ApplicationContext)中;
4. 使用 Bean。

### Spring IOC 支持的功能
* 依赖注入
* 依赖检查
* 自动装配
* 支持集合
* 指定初始化方法和销毁方法
* 支持回调方法

### 依赖注入的四种方法
从注入方法来看，Ioc可以分为三种类型：构造函数注入、Setter注入和接口注入。
Spring主要是构造函数注入和Setter注，接口注入不推荐使用。

####  构造器注入
1. 依赖于构造方法实现，将被依赖对象通过构造函数的参数注入给依赖对象，并且在初始化对象的时候注入。
2. 优点：
对象初始化完成后便可获得可使用的对象。
3. 缺点：
不够灵活，特别是在参数很多的情况下，维护复杂。
 
#### setter注入
1. 最主流的方式，利用了Bean定义的setter方法来完成注入，灵活且可读性高，基于反射实现的。
IoC Service Provider通过调用成员变量提供的setter函数将被依赖对象注入给依赖类。
2. 优点：
灵活。
3. 缺点：
依赖对象初始化完成后由于尚未注入被依赖对象，因此还不能使用。

#### 接口注入
1. 有时资源并非来自系统，而是来自外界，如数据库连接资源，可以用接口注入的方式获取它。
依赖类必须要实现指定的接口，然后实现该接口中的一个函数，该函数就是用于依赖注入。该函数的参数就是要注入的对象。
2. 优点
接口注入中，接口的名字、函数的名字都不重要，只要保证函数的参数是要注入的对象类型即可。
3. 缺点
侵入行太强，不推荐。

#### 基于注解的方式
例如 @Autowired 注解

## IoC 容器
Spring 提供了两种容器类型 BeanFactory 和 ApplicationContext。
1. BeanFactory，基础类型的容器接口，默认采用 lazy-load，只有需要访问某个 bean 时才会进行 bean 的初始化和依赖注入。启动速度快，需要资源少，功能也较少；
    * 提供了 IOC 的配置机制
    * 包含 Bean 的各种定义，便于实例化 Bean；
    * 建立 Bean 之间的依赖关系；
    * Bean 生命周期的控制。
2. ApplicationContext，继承自 BeanFactory 接口，是高级容器实现。除了 BeanFactory，还继承了很多其他的接口，提供了 BeanFactory 外更多高级的功能。默认会在容器启动后对所有的 bean 全部进行初始化和装配，需要更多的资源，初始化时间也会更久；
    * BeanFactory：能够管理和装配 Bean；
    * ResourcePatternResolver：能够加载资源文件；
    * MessageSource:能够实现国际化等功能；
    * ApplicationEventPublisher：能够注册监视器，实现监听机制。
3. BeanDefinition, 接口，定义了 Bean 的各种属性，包括 id，name，class 等等；
4. BeanDefinitionRegistry，用于操作 BeanDefinition(会放在一个 ConcurrentHashMap 中)；
5. BeanFactory 和 ApplicationContext 的比较：
    * BeanFactory 是 Spring 框架的基础设施，面向 Spring；
    * ApplicationContext 面向使用 Spring 框架的开发者。
    
### BeanFactory
核心方法是 getBean()，其代码逻辑为：
    * 转换 beanName()；
    * 从缓存中加载实例；
    * 实例化 Bean；
    * 检测 parentBeanFactory
    * 初始化依赖的 Bean。
    
### 总结
装配可以分为两个阶段，分别是容器启动阶段和 Bean 实例化阶段。
1. 容器启动
容器启动时，首先会通过某种方式加载配置元信息，如 xml 配置就是依赖某些工具类(BeanDefinitionReader)对加载的元信息进行解析和分析，并生成对应的 BeanDefinition, 最后将这些保存了 bean 定义信息的 BeanDefinition 注册到相应的
BeanDefinitionRegistry中(本质上是一个Map)。此时容器初始化过程完成；
2. Bean 实例化
当某个请求通过容器的 getBean() 方法请求某个对象时，或因依赖关系容器隐式地调用 getBean() 方法时，会触发第二阶段的活动。
首先会检测该对象是否被初始化，若没有则根据注册的 BeanDefinition 提供的信息进行实例化，并为其注入依赖(构造器或者setter)。在对象装配完毕后，将其返回给调用者。

### Bean 的实例化过程
1. 实例化 bean 对象；
    * 实例化时采用策略模式决定以何种方式初始化 bean 实例，通过反射或者是 CGLib 动态字节码生成 bean 实例。 
2. 设置对象属性；
3. 检查 Aware 相关接口，并设置相关依赖；
4. BeanPostProcessor 前置处理；
5. 检查是否是 InitializingBean 以决定是否调用 afterPropertiesSet 方法；
6. 检查是否配置有自定义的init-method;
7. BeanPostProcessor 后置处理；
8. 注册必要的 Destruction 相关回调接口；
9. 使用 Bean
10. 是否实现 DisposableBean 接口；
11. 是否配置有自定义的 destroy 方法

### ApplicationContext
#### Spring 中的 Resource
核心方法是 getInputStream() 获取资源输入流
是 Spring 中所有资源的抽象和访问接口，主要有以下实现类：
1. ByteArrayResource，将字节数组提供的数据作为一种资源进行封装；
2. ClassPathResource, 从 Java 应用程序的 ClassPath 中加载具体的资源进行封装，通过指定的类加载器(ClassLoader)
或者给定的资源进行加载；
3. FileSystemResource，从 java.io.File 中获取；
4. URLResource，通过 java.net.URL 进行资源的查找；
5. InputStreamResource，通过给定的 InputStream 视为一种资源实现类，使用较少；
6. 自定义，继续实现 Resource 接口。
#### ResourceLoader
核心方法是：
* getResource() 获得 Resource
* getClassLoader() 获取类加载器
用于查找和定位定位资源的接口，是资源查找接口的统一抽象，具体的加载策略由其子类实现。
1. DefaultResourceLoader
默认实现子类，其 getResource(String location) 查找资源的逻辑如下：
* 判断 location 是否是以 '/' 开头，如果是则使用 ClassPathContextResource;
* 否则，判断是否是以 'classpath:', 如果是则使用 ClassPathResource；
* 否则，在使用 FileUrlResource 或 UrlResource。
2. FileSystemResourceLoader
继承自 DefaultResourceLoader ，是从 FileSystemContextResource 加载资源的；
3. ClassRelativeResourceLoader
继承自 DefaultResourceLoader
4. ResourcePatternResolver 接口
继承自 ResourceLoader， 用于批量查找 ResourceLoader。

#### ApplicationContext 相对于 BeanFactory 的增强
1. 统一的资源加载策略；
2. 国际化信息支持；
3. 容器类时间发布；
4. 多配置文件加载功能。

## 装配Bean的方式
Spring装配Bean有三种方式进行配置：
* 在XML中显式配置
* 在Java接口和类中实现配置
* 隐式Bean的发现机制和自动装配

### Bean的装配方式的选择
1. 基于约定优于配置的原则，最优先的应该是通过隐式Bean的发现机制和自动装配原则。
2. 无法使用自动装配的情况下优先考虑使用Java接口和类中实现配置，可以避免XML配置的泛滥。
3. 在上述方法都无法使用时才考虑选择使用XML去配置Spring Ioc容器。

### 基于XML的配置
使用XML装配Bean需要定义对应的XML(XSD)文件，会定义Bean的一些元素，
* id属性 Spring找到这个Bean的编号，非必须，如果没有声明id， Spring会采用全限定名#{numberl}的格式生成编号
* class是一个类的全限定名，用于找到这个类
* 如果是构造器注入，需要使用 <constructor-arg>
* 如果是 setter 注入，property元素定义类的属性，其中name定义的是属性名称，value是其值。
* List、Map、Set等复杂属性
* 还可以通过命名空间进行装配。

### 通过注解装配Bean
Spring提供了两种方式让Spring Ioc容器发现Bean
* 组件扫描：通过定义资源的方式，让Spring Ioc容器扫描对应的包，从而装配Bean
* 自动装配：通过注解定义，使得一些依赖关系可以通过注解完成

1. 使用 @Component 装配 Bean
使用Component代表Spring Ioc会把这类扫描成Bean实例，value属性表示Bean的id。
@Value表示值的注入
使用@ComponentScan进行包扫描，默认扫描当前路径

2. 自动装配 @Autowired
推荐使用, 基于类型。

3. 自动装配的歧义性
@Primary
当Spring Ioc通过一个接口或者抽象类进行注入时，如果有多个实现类，可以通过@Primary避免混淆
@Qualifier
默认情况下自动装配是根据类型进行装配的，@Qualifier采用名称进行查找，可以避免歧义

4. 使用@Bean装配
@Component只能用在类上，@Bean可以注解到方法上，并且将返回的对象作为Spring的Bean存放在Ioc容器中

5. JSR250
* @Resource，作用与 @Autowired 类似，遵循的是 byName 自动绑定的行为准则；
* @PostConstruct 在对象构造之前调用；
* @PreDestroy 在对象销毁之后。

6. 自动扫描
配置包自动扫描，<context:component-scan base-package="cn.abelib.XXX.XXX">，会自动扫描包下面的
所有的类定义。默认扫描的是所有被 @Component 修饰的类，包括 @Repository，@Service，@Controller等注解。

## Spring Bean的作用域
* 单例，singleton， 默认选项，在整个应用中，Spring只生成一个Bean实例，生命周期与 Ioc 容器的生命周期相同。
需要注意的是，与 GOF 设计模式中 singleton 的区别，前者是整个 IoC 容器中只有一个实例，而后者是同一个 ClassLoader
中只有一个实例；
* 原型，prototype，每次注入或者获取Bean时(getBean())，Spring Ioc都会为它创建一个新的实例；
* 会话，session，在Web中使用，每次会话，Spring Ioc都会为它创建一个新的实例；
* 请求，request，在Web中使用，每次请求Spring Ioc都会为它创建一个新的实例， 但是不同请求会创建不同的实例；
* 全局会话，globalSession，在Web中使用，仅对 Portlet 有效；
* 自定义 scope

### Spring Bean 的线程安全性问题
Spring 的 Bean 并没有保证线程安全，需要自己去实现相关的功能。
但是大多数情况下 Spring 的 Bean 是无状态的，所以是线程安全的。

