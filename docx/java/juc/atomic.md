# Atomic 
## 原理
以 AtomicInteger 为例，AtomicInteger 是 int 的一个封装，提供原子性的访问更新操作，其原子性操作的实现是基于 CAS
技术实现的。

## CAS 
### 什么 CAS
CAS 操作包含三个操作数，内存位置(V), 预期原值(A)和新值(B)。如果内存位置的值与预期原值相同，那么就更新到新值，其底层
是基于 sun.misc.UnSafe 包中的 native 方法实现的。CAS 是 Java 中无锁化实现的基础。

### CAS 的优势
CAS 可以避免使用互斥锁，避免线程频繁的挂起，从而提高性能。

### CAS 的缺点
1. ABA 问题，可以通过版本号解决，如 juc 中的AtomicStampedeReference；
2. 循环开销大，如果 CAS 一直处理不成功，会一直消耗 CPU 资源；
3. 只能保证一个共享变量的原子性操作，可以使用 AtomicReference 把多个变量放到一个对象中进行处理。