# Spring AOP
AOP可以认为是对面向独享编程的一个补充，通过横向切割的方式对代码进行抽象，可以将一些公共的操作从业务逻辑中抽取出来。

## AOP术语
1. 连接点(Joinpoint)
指哪些目标类的方法可以被拦截

2. 切点(Pointcut)
指对JoinPoint中的哪些目标类方法进行切入

3. 增强(Advice)或叫增强
增强是织入目标类连接点上的一段程序代码，指在某个特定点的pointcut上需要执行的动作如
日志记录、权限验证等具体要应用带切入点的代码
* Before Advice
* After Advice
* Around Advice   

4. 目标对象(Target)
增强逻辑的织入目标类。

5. 引介(Introduction)
引介是一种特殊的增强，它为类添加了一些属性和方法。即使一个业务类本没有实现某个接口，通过AOP的引介功能，可以动态地为该业务类添加接口的实现逻辑，让业务类成为这个接口的实现类。

6. 织入(Weaving)
织入是将增强添加到目标类的具体连接点上的过程。AOP就是将目标类，增强或者引介编织到一起。
根据不同的实现技术，AOP有三种织入方式：
    1. 编译器织入，需要使用特殊的Java编译器
    2. 类装载器织入，需要使用特殊的类加载器
    3. 动态代理织入，在运行期为目标类添加增强生成子类的方式。
Spring采用动态代理织入，而AspectJ采用编译期织入和类装载织入，以注解的形式实现的AOP。

7. 代理(Proxy)
一个类被AOP织入增强后，就产生了一个结果类，它是融合了原类和增强逻辑的代理类，根据不同的代理方式，代理类可能是和原类具有相同接口的类，也可能就是原类的子类，所以可以采用与调用原类相同的方式调用代理类。

8. 切面(Aspect)
切面是由切点和增强(引介)组成，既包括横切逻辑的定义，也包括连接点的定义。

## AOP实现方式
1. AspectJ
AspectJ是语言级别的AOP实现，需要专门的编译器(ajc编译器)来生成。

2. Spring AOP
Spring AOP使用纯Java实现，不需要专门的编译过程，也不需要特殊的类装载器，它在运行期通过代理方式向目标类织入增强代码。Spring中可以无缝的将Spring AOP、Ioc和AspectJ整合在一起。

## 基础知识
Spring AOP提供了两种代理机制：JDK 动态代理和 CGLib 字节码动态代理技术。Spring AOP 会优先使用 JDK 动态代理(性能会更高)，如果被代理类没有实现接口，就使用 CGLIB动态代理。
### JDK动态代理
Java提供了动态代理技术，允许开发者在运行期间创建接口的代理实例。主要使用的是 Proxy和InvocationHandler。其中
InvocationHandler是一个接口，可以通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态地将横切逻辑和业务逻辑编织在一起。
Proxy利用InvocationHandler动态创建一个符合某一个接口的实例，生成目标类的代理对象。
JDK动态代理必须借助一个接口才能实现，主要分为两步：
1. 建立代理对象和真实对象之间的关系，借助
```Java
Proxy.newProxyInstance(target.getClass().getClassLoader(),
        target.getClass().getInterfaces(),
        handler);
```
其中newProxyInstance包含三个参数
* 第一个是类加载器，我们使用了目标对象本身的类加载器
* 第二个是把生成的动态代理对象挂在那个接口下，即目标对象所实现的接口
* 第三个是定义实现方法逻辑的代理类，必须实现InvocationHandler接口的invoke()方法。
2. 实现代理逻辑
```
invoke(Object proxy, Method method, Object[] args)
```
invoke()方法可以实现代理逻辑方法，invoke()方法可以实现代理逻辑，其三个参数分别是
* proxy，代理对象
* method， 当前调度的方法
* args， 调度方法的参数

### CGLIB动态代理
因为JDK动态代理只能在提供接口后才能使用，所以在没有提供接口的环境下，可以使用CGLIB动态代理，其优势在于不需要提供接口，只需要一个费抽象类就可以实现动态代理。
CGLIB动态代理采用底层的字节码技术，通过继承的方式为一个类创建子类，在子类中采用方法拦截的技术拦截所有父类方法的调用并顺势织入横切逻辑。由于CGLIB采用动态创建子类的方式生成代理对象，所以不能对目标类中的final或者private方法进行代理。

### 两种代理方式的选择
CGLib代理的性能比JDK动态代理高很多(在 jdk6 以后，JDK 动态代理性能会更高)，但是所花费的时间同样也比JDK动态代理高很多。
对于singleton的代理对象或者具有实例池的代理，因为无须频繁的创建代理对象，所以比较适合用CGLib代理反之则适合用
JDK动态代理。

## Spring AOP
### Spring AOP 的织入
使用类 ProxyFactory 作为织入器进行织入。

### Spring对AOP的支持
Spring AOP是一种基于方法拦截的AOP，或者说Spring只能支持方法拦截的AOP。Spring提供了4种方法实现AOP的拦截功能：
* 使用ProxyFactoryBean和对应的接口实现AOP
* 使用XML配置AOP
* 使用@AspectJ注解驱动切面
* 使用AspectJ注入切面

### @AspectJ 注解开发 Spring AOP
* before 目标方法执行前执行，前置通知
* after 目标方法执行后执行，后置通知
* after returning 目标方法返回时执行 ，后置返回通知
* after throwing 目标方法抛出异常时执行 异常通知
* around 在目标函数执行中执行，可控制目标函数是否执行，环绕通知

### 使用XML配置开发Spring AOP

## AOP 的应用
1. 异常处理

2. 安全检查

3. 缓存

4. 日志

5. 事务


















