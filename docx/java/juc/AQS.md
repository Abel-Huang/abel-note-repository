## AQS 
AbstractQueuedSynchronizer
### AQS的设计
* 使用Node实现FIFO队列，可以用于构建锁或者其他同步装置的基础框架，AQS 的队列是一个双向队列，多线程争用资源时会进入
此队列；
* 利用了一个int类型表示状态，即 state，（如在ReentrantLock，state为0表示未获取锁，1表示获取到锁，大于1表示重入锁的数量；
* 使用方法时继承(AbstractQueuedSynchronizer本身是一个抽象类)
* 子类通过继承并通过实现它的方法管理其状态{acquire 和 release}的方法操纵状态
* 可以同时实现排它锁和共享锁模式(独占 Exclusive、共享 Share)


### AQS同步组件
* CountDownLatch，通过计数判断线程是否需要一直阻塞；
* Semaphore，控制同一时间并发线程的数目；
* CyclicBarrier
* ReentrantLock
* Condition
* FutureTask

### CountDownLatch
1. CountDownLatch 允许一个或多个线程等待其他线程完成操作，是一个共享锁。
作用类似于join，CountDownLatch构造时需要输入一个int类型的参数作为计数器，每次调用
countDown()方法时，N就会减一，CountDownLatch的await()就会阻塞当前线程，直到N减为0.
2. 实现原理：
* CountDownLatch 将任务分为 N 个子线程去执行， state 也初始化为 N (注意 N 与线程个数一致)；
* 这个 N 个子线程是并行执行的，每个子线程执行完后 countDown() 一次， state 会 CAS 减一；
* 等到所有的子线程执行完之后(state = 0), 会 unpark() 调用线程，恢复；
* 主线程就会从 await() 函数返回，继续剩余动作。

### Semaphore
用来控制并发线程数, 例如用来限制连接池的最大连数。由于 Semaphore 适用于限制访问某些资源的线程数目，因此可以用来做限流。
acquire() 获取一个许可
acquire(int n) 获取多个许可
try
tryAcquire() 尝试获取一个许可
tryAcquire(int n, TimeUnits) 尝试等待一定的时间来获取一个许可

### Condition
Condition 则是将 wait、notify、notifyAll 等操作转化为相应的对象方法，分别对应为 await(), signal(),
signalAll()

### CyclicBarrier
让一组线程到达一个同步点时被阻塞，直到最后一个线程达到屏障时，
屏障才会打开，所有被屏障拦截的线程才会继续执行。

#### CountDownLatch和CyclicBarrier的区别
1. CountDownLatch的计数器只能使用一次，CyclicBarrier的计数器可以使用多次
2. CountDownLatch是一个或多个线程等待其他线程完成后才能继续执行，
CyclicBarrier实现的是一组线程在同步点是相互阻塞。

### ReentrantLock和锁
1. ReentrantLock 实现原理
* AQS(即 Sync 内部类)state 初始化为0， 表示未锁定状态；
* A 线程 lock() 时，会调用 tryAcquire() 独占该锁并将 state + 1；
* 在此之后，其他线程再 tryAcquire() 时就会失败，直到 A 线程 unlock() 到 state=0(unlock，会使 state -1)，
其他线程才有机会获得该锁；
* 在释放锁之前，A线程可以重复获取此锁(state会累加)，这就是可重入的概念；
* 需要注意获取次数一定要和释放次数相等，才能彻底将锁释放。

2. 公平锁和非公平锁的实现
公平锁会使用 AQS 的队列，而非公平锁直接就是用 CAS 进行处理了。
    
2. ReentrantLock和synchronized锁
* 两者都是可重入锁(递归锁)， 同一个线程进入时计数器自增1，直到计数器变为0时才能释放锁
* 锁的实现，synchronized是基于JVM实现的，ReentrantLock是JDK实现的
* 性能的区别，在synchronized进行优化之前，synchronized的性能比ReentrantLock差很多，
后来版本的synchronized引入偏向锁，轻量级锁和自旋锁的机制后(优化思路借鉴了cas的思想
尽可能的避免进入锁)，二者几乎没有产生区别了。官方推荐使用synchronized关键字，因为其操作更简单。
* 功能区别
    * 便利性，synchronized加锁和释放锁由JVM完成，ReentrantLock的加锁和释放需要用户自己手动
    控制，使用不当忘记释放就会造成死锁。
    * 细粒度和灵活度，ReentrantLock会更优秀
* ReentrantLock独有的功能
    * 可指定是公平(先等待的线程现先获得锁)锁还是非公平锁，而synchronized关键字只能够为非公平锁
    * 提供了一个Condition类，可以分组唤醒需要唤醒的线程，而synchronized关键字只有全部唤醒或者全部不唤醒所有线程
    * 提供能够中断等待锁的线程的机制， lock.lockInterruptibly(), ReentrantLock是一种自旋锁，通过循环调用CAS实现
    加锁，避免线程进入内核状态。
    * 当需要使用ReentrantLock这三个功能时需要使用ReentrantLock，其他情况可以使用synchronized。
3. ReentrantReadWriteLock
读写锁，可以分别对读和写进行操作，需要注意的是，由于采用写锁采用悲观锁机制，只要发生读操作就会有写锁，
在读很多写很少的情况下写操作容易造成饥饿，即写操作一直处于等待中。
4. StampedLock
JDK1.8中新添加的类，加锁时会返回一个long新的stamp的值
5. Condition条件类
用于线程之间的通信