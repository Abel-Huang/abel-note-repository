# Java 的锁

## synchronized
synchronized 是 jdk 内置的锁，在并发包出现出现前是 Java 锁的唯一选择，从 jdk1.6 开始 synchronized 进行了大量的优化，如偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化等技术来减少锁操作的开销，性能已经和 ReentrantLock 接近。
在 jdk 1.6 中，锁一共有4种状态：无锁状态，偏向锁，轻量级锁，重量级锁，会随着竞争情况逐渐升级，锁可以升级但是不能降级，目的是为了提高获得锁和释放锁的效率。

### 偏向锁
偏向锁实际上并不是锁，只是在对象头里面增加一个偏向锁的标志位，是用于没有竞争的情况。

### 轻量级锁
基于自旋锁来实现，在自旋时会消耗 CPU。
#### 自旋锁
1.基于乐观情况下推荐使用，即锁竞争不强，锁等待时间不长的情况下推荐使用；
2.单cpu无效，因为基于cas的轮询会占用cpu,导致无法做线程切换；
3.轮询不产生上下文切换，如果可估计到睡眠的时间很长，用互斥锁更好。
### 重量级锁
真正的锁，会阻塞线程，不会消耗 CPU 资源。
### 锁的膨胀和升级
偏向锁 -> 轻量级锁 -> 重量级锁(互斥锁)
### 使用
* 使用 synchronized 修饰非静态方法以及 synchronized(this) 代码块使用的是对象锁；
* 使用 synchronized 修饰静态方法以及 synchronized(class) 代码块使用的是类锁；
* 在类的类的内部声明一个私有属性如 private Object lock，在需要加锁的同步块使用 synchronized(lock) 表示是一个似有所。
### 特点
* 对象锁具有可重入性；
* 当一个线程获得了某个对象的对象锁，则该线程仍然可以调用其他任何所需要该对象锁的 synchronized 方法或者 synchronized(this) 代码块；
* synchronized(this) 代码块只能被一个线程同时访问，其他线程访问时将会被阻塞；
* 每个类只持有一个类锁，每个对象对应一个对象锁；
* 类锁和对象不会互相竞争；
* 似有所和对象锁也不会产生竞争；
* 使用私有锁可以减小锁的粒度，减小由锁产生的开销。

## 死锁
多个线程(或依赖共同资源的进程)彼此循环依赖资源导致会一直处于等待之中，所有的实体都不能继续执行，处于阻塞的状态
就被称为死锁。
在Java程序中可以通过 jstack 进行死锁的定位。

### 死锁发生的条件
1. 互斥条件，资源都是互斥的；
2. 互斥条件是长期持有的，在使用结束之前自己不会释放，也不会被其他线程抢占；
3. 循环依赖，两个或者多个线程之间出现了锁的链条环。

### 避免死锁
1. 避免使用多个锁，只有在需要时才会持有锁；
2. 如果必须使用锁，需要设计好锁的获取顺序；
3. 使用带超时的锁，为程序带来更多的可控性；
4. 可以通过一些代码分析的方法去进行锁的判断。

## AQS 中的锁
### ReentrantLock 重入锁
特性：
* 公平性，支持公平锁和非公平锁，默认是非公平锁；
* 可重入；
* 可中断，相对于 synchronized 不能中断，而 ReentrantLock 可以对中断做出响应；
* 超时机制，超时后不会获得锁，不会造成死锁。
ReentrantLock 和 synchronized 都是一种悲观锁。
### ReentrantReadWriteLock 读写锁
特性：
* 公平性，支持公平锁和非公平锁，默认是非公平锁；
* 可重入，读线程在读取锁之后能够再次获得读锁；写线程在获取写锁之后能够再次获得写锁，同时也可以获得读锁(锁降级)；
* 锁降级： 先获取写锁，再获取读锁，锁降级是为了保证数据的可见性。

### Condition 条件判断
用于替代传统的 Object 类中的 wait(), notify(), notifyAll() 等方法。Condition 必须与 Lock 一起使用，一个 Condition 实例必须与一个 Lock 相绑定。
特性:
Condition 在使用 await,signal,signalAll 时必须获得 Lock 的 lock()(Object 类中的 wait(), notify(), notifyAll() 必须在 synchronized 代码块中执行)；
* 支持响应中断；
* 支持定时唤醒。
 
### LockSupport 基于线程的锁
park() 和 unpark() 方法表示阻塞和非阻塞，对应与 wait() 和 notify()，但是会更加灵活，并且
不用考虑 park() 和 unpark() 的顺序，他们的顺序是不会影响结果的。