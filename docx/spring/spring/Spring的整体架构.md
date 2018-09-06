## Spring的整体架构
Spring的核心组件只有三个:Core、Context和Bean
* Bean是Spring的关键因素，Spring将对象包裹在Bean中从而达到管理这些对象以及做一系列额外的操作
* Context给Bean提供运行时的数据，为Bean发现和管理Bean之间的关系，Context可以认为是Bean关系的集合，这个关系称为Ioc容器。
* Core可以认为是发现、建立和维护每个Bean之间的关系所需要的一系列工具。

### Bean组件
1. 
顶级接口是BeanFactory，有三个子接口分别是ListableBeanFactory可以列表的，HierarchicalBeanFactory可继承的，AutowireCapableFactory定义了Bean的自动装配规则，这四个接口定义了Bean的集合、Bean之间的关系和Bean的行为。
2. 
Bean的定义由BeanDefinition对象定义，Spring成功解析了定义的<bean/>节点后，在Spring的内部就会被转化为BeanDefinition对象。
Bean的解析主要就是对Spring配置文件的解析，用BeanBeanDefinitionReader的实现类进行解析

### Context组件
Context组件就是给Spring提供一个运行时的环境，用意保存各个对象的状态。
ApplicationContext是Context的顶级父类，继承了BeanFactory，说明Spring容器的运行主体对象是Bean，ApplicationContext继承了ResourceLoader接口，可以访问到任何外部资源。
ApplicationContext的子类主要包含两个方面：
* ConfigurableApplicationContext表示该Context是可以修改的，在构建Context中用户可以动态添加或修改已有的配置信息；
* WebApplicationContext是为web准备的Context可以直接访问ServletContext，一般使用较少。
ApplicationContext的主要功能有：
* 标识一个应用环境；
* 利用BeanFactory创建Bean对象；
* 保存对象关系表
* 能够捕获各种事件
Context作为Spring的Ioc容器，整合了Spring的大部分功能。

### Core组件 
Core组件一个重要的组成部分就是定义了资源的访问方式。通过ResourceLoader的子类ResourcePatternResolver,
Context把资源的加载、解析和描述工作委托给ResourcePatternResolver完成，相当于一个接头人，把资源的加载、解析和
资源的定义整合在一起便于其他组件使用。
#### Ioc容器工作流程
##### 创建BeanFactory工厂
Ioc容器实际上是Context组件结合其他两个组件共同构建了一个Bean关系网,AbstractApplicationContext的refresh方法就是构建这个关系网的入口，整个方法就是构建整个Ioc容器过程。
主要步骤如下：
1. 构建BeanFactory，以便于产生所需要的Bean
    * 通过refreshBeanFactory()，如果BeanFactory存在就销毁，然后再继续创建，如果不存在则直接进行创建。
    * 除了创建BeanFactory还有与Bean注册相关的类，如在loadBeanDefinitions中会进行加载、解析Bean的定义，
    也就是把用户定义的数据结构转化为Ioc容器中特定的数据结构。     
2. 注册有需要的实例事件
3. 创建Bean实例对象
4. 触发被监听的事件

##### 创建Bean实例并且构建Bean的关系网
Bean的实例化是由AbstractApplicationContext的finishBeanFactoryInitialization方法开始的。Bean的实例化是在BeanFactory中完成的。
特殊的Bean->FactoryBean，这是一个可以产生Bean的Bean。

##### 针对Ioc容器进行扩展
1. 对于Spring的Ioc容器而言，主要有BeanFactoryPostProcessor和BeanPostProcessor，分别在构建BeanFactory和构建Bean对象时调用，InitializingBean和DisposableBean，分别是在
Bean构建和销毁时被销毁，用户可以实现在这些接口定义的方法，Spring会在适当的时候调用他们。
2. FactoryBean是一个特殊的Bean，用户可以进行更多的控制。

##### 总结
使用Spring必须要先构建Ioc容器，没有Ioc容器Spring将无法工作，ApplicatioContext.xml就是Ioc容器的默认配置文件，Spring的所有特性功能都是基于Ioc容器的，例如AOP。









































