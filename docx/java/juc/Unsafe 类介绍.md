# Unsafe 类介绍
## 如何获得 Unsafe 类实例：
在 Unsafe 类的定义中，只有引导类加载器 'BootstrapClassLoader' 加载时才合法，否则会抛出
SecurityException。因此在 jdk 中，可以直接通过 Unsafe.getUnsafe() 静态方法获得实例，
在其他类中可以通过以下两种方式获得：u
1. 使用命令行命令 '-Xbootclasspath/a' 把调用 Unsafe 相关方法的类 A 所在的 jar 包加到
默认的 bootstrap 路径中，使得该类被引导类加载器加载。
2. 通过反射获取单例对象，目前主流的使用方法(参考 Disruptor)
```java
  try {
        Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
        theUnsafe.setAccessible(true);
        return (Unsafe) theUnsafe.get(null);
      } catch (Exception e){
        throw new RuntimeException("Unable to load unsafe", e);
      }
```

## 主要功能
### 内存操作
直接针对堆外内存进行操作。
* 堆外内存的分配，相当于 C++ 的 malloc() 函数：
    `public native long allocateMemory(long bytes)`
* 拷贝
* 释放
* 内存扩充
* 给定地址值操作
#### 堆外内存的原因
* 减少垃圾回收的停顿；
* 提高 IO 操作的性能。
#### 应用
NIO 中的 DirectByteBuffer 就是通过 Unsafe 的堆外内存实现的。

### CAS
用于原子操作的实现，基于 底层 CPU 指令 cmpxchg。
* compareAndSwapObject()
* compareAndSwapInt()
* compareAndSwapLong()
#### 应用
* 原子类 AtomicXXX
* 并发包中的各种无锁实现

### 线程调度
* unpark() 取消线程阻塞
* park() 阻塞线程
* monitorEnter() 获得(重入锁)
* monitorExit() 释放锁
* tryMonitorEnter() 尝试获得(重入锁)
#### 应用
* LockSupport 中的 park() 和 unpark()

### Class 相关
提供 Class 和他的静态字段操作相关方法，包含静态字段内存定位，定义类，定义匿名类，检验和确保初始化等。

#### 应用
从 JDK8 开始，JDK 使用 invokedynamic 以及 VM Anonymous Class 实现 lambda 表达式。
* invokedynamic：jdk7 引入的新的虚拟机指令，用于 JVM 上的动态语言。
* VM Anonymous Class：虚拟机匿名类，可以由 Unsafe.defineAnonymousClass() 来创建。

### 对象操作
主要用于对象成员属性相关操作以及非常规的对象实例化方式等相关方法。


### 数组相关

### 内存屏障

### 系统相关