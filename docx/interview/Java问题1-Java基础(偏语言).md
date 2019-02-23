## Java问题1-Java基础(偏语言)
1. 关于Java的泛型数组。
Java泛型不支持泛型数组，因为Java的编译期泛型和类型擦除，泛型数组在运行期间无法对数组中的类型进行检查，如果添加了不支持的类型，Java无法发现这个错误，会导致抛出ClassCastException。

2. 关于泛型的本质
泛型的本质是类型参数化，Java实现泛型的时候采用的类型擦除的泛型，在编译后生成的字节码中是不存在具体的类型的，Java的泛型是一种语法糖。

3. Java内部类
Java语言中之所以引入内部类，是因为有些时候一个类只在另一个类中有用，我们不想让其在另外一个地方被使用。内部类之所以是语法糖，是因为其只是一个编译时的概念，一旦编译完成，编译器就会为内部类生成一个单独的class文件，名为outer$innter.class。

4. Java的枚举
java中类的定义使用class，枚举类的定义使用enum。在Java的字节码结构中，其实并没有枚举类型，枚举类型只是一个语法糖，在编译完成后被编译成一个普通的类。这个类继承java.lang.Enum，并被final关键字修饰。所以枚举类型是不可变类型。

5. 泛型的通配符
    * 通配符的设计存在一定的场景，例如在使用泛型后，首先声明了一个Animal的类，而后声明了一个继承自Animal类的Cat类，显然Cat类是Animal类的子类，但是List<Cat>却不是List<Animal>的子类型，
    而在程序中往往需要表达这样的逻辑关系。为了解决这种类似的场景，在泛型的参数类型的基础上新增了通配符的用法，具体来说有三种用法：<? extends T>、<? super T>、<?>。其中前两者被称为限定通配符，<?>被称为非限定通配符。 
    * <? extends T> 上界通配符: 上界通配符顾名思义，<? extends T>表示的是类型的上界（包含自身），因此通配的参数化类型可能是T或T的子类。正因为无法确定具体的类型是什么，add方法受限（可以添加null，因为null表示任何类型），   
    但可以从列表中获取元素后赋值给父类型。如上图中的第一个例子，第三个add()操作会受限，原因在于List<Animal>和List<Cat>是List<? extends Animal>的子类型。
    * <? super T> 下界通配符:下界通配符<? super T>表示的是参数化类型是T的超类型（包含自身），层层至上，直至Object，编译器无从判断get()返回的对象的类型是什么，因此get()方法受限。但是可以进行add()方法， 
     add()方法可以添加T类型和T类型的子类型，如第二个例子中首先添加了一个Cat类型对象，然后添加了两个Cat子类类型的对象，这种方法是可行的，但是如果添加一个Animal类型的对象，显然将继承的关系弄反了，是不可行的。
    * <?> 无界通配符: 在理解了上界通配符和下界通配符之后，其实也自然而然的理解了无界通配符。无界通配符用<?>表示，?代表了任何的一种类型，能代表任何一种类型的只有null（Object本身也算是一种类型，
     但却不能代表任何一种类型，所以List<Object>和List<null>的含义是不同的，前者类型是Object，也就是继承树的最上层，而后者的类型完全是未知的）。

6. Java中8种基本的数据类型
short，int，float，double，long，char，byte，boolean对应的引用类型分别是
Short，Integer，Float，Double，Long，Character，Byte，Boolean

7. switch是否支持String
jdk1.7后开始支持，本质上switch语句后面的括号只能是int型，但是编译器将其自动转为int型，String会取其hashcode，本质上是一个语法糖。

8. Java四种引用方式
强引用，软引用，弱引用，虚引用。
一般的new创建对象都是强引用，一个对象只要有强引用就不会回收；
SoftRefererence，如果内存充足不会回收，如果内存不足就不会回收；
WeakReference，弱引用，垃圾回收器线程扫描到只具有弱引用的对象，都会回收期内村；
PhantomReference，仅具有虚引用的对象随时会被垃圾回收器回收。

9. HashTable和ConcurrentHashMap的选择
HashTable和ConcurrentHashMap都可以实现线程安全，HashTable在高并发下由于锁的原因，性能会急剧下降，而ConcurrentHashMap依然能保证不错的性能，但是ConcurrentHashMap是弱一致性的，即put了新的元素，对外界来说不一定是立即可见的。

10. try catch finally语句中try中有return，finally会执行么？
当程序执行try{}遇到return时，程序会先执行return语句，但并不会立即返回——也就是把return语句要做的一切事情都准备好，也就是在将要返回、但并未返回的时候，程序把执行流程转去执行finally块，当finally块执行完成后就直接返回刚才return语句已经准备好的结果。
只有在try{}块中包含遇到System.exit(0)。 之类的导致Java虚拟机直接退出的语句才会不执行。

11. Java异常的分类
Java将可抛出(Throwable)的结构分为三种类型：
被检查的异常(Checked Exception)。 运行时异常(RuntimeException)。 错误(Error)。
检查异常必须要try catch捕获才能通过编译，运行时异常不需要，出现异常时由虚拟机捕获。

12. 静态代码块的执行时间
静态代码块中的执行时间是在类加载的时候进行的，并且只会执行一次。
静态代码块是最先执行的(先父类，然后子类)，然后执行父类的非静态部分，最后执行子类的非静态部分。

13.  Java如何实现动态代理机制  
    * JDK动态代理：代理类和目标类实现共同的接口，使用InvocationHandler, 底层是基于
    反射；  
    * CgLib动态代理：代理类是目标类的子类，用到MethodInterceptor接口，底层基于字节码技术(asm)

14. xml解析方式
    * DOM
    * SAX

15. String，StringBuffer和StringBuilder
    * String是不可变的，每次修改时都会重新生成一个新的对象，然后将引用指向这个新的对象(频繁修改String的内容会造成大量的垃圾)；
    * StringBuilder和StringBuffer都是可变的对象，适用于频繁修改的对象，StringBuilder是非线程安全的，适用于单线程下，速度更快，StringBuffer是线程安全的(在方法上加上synchronized关键字)，适用于多线程环境下。

16. 32位系统和64位系统中int的长度
Java中，int类型变量的长度是一个固定值，与平台无关，均为32位即4个字节，32位系统和64位系统中int的长度相等。取值范围是(-2^31~2^31-1, 因为int是有符号数，必须考虑正负号)

17. a==b 和 a.equals(b) 的区别(a b都是对象)
a==b 是比较两个对象的引用，只有a和b指向堆中的同一个实例时才会返回true，a.equals(b)进行逻辑比较，通常用与其具体的实现有关，根据Java规范，使用equals()必须保证两者有相同的hashcode。

18. 编译时常量和运行时常量
通过final修饰的就是常量，一旦复制就不能进行修改，常量又分为编译时常量和运行时常量，编译时常量在编译时就被计算，编译后的符号表中不会有这个常量名而是直接以常量值代替，而运行时常量的初始化在运行之后才能确定。
静态代码块的执行(在类加载时执行)在编译时常量之后，在运行时常量之前。

























