# JVM 的内存分区

JVM 运行时内存主要分成五个部分，分别是：
1. 方法区，Method Area
2. 堆，Heap
3. 虚拟机栈，VM Stack
4. 本地方法栈， Native Method Stack
5. 程序计数器， Program Counter Register

其中方法区和堆是线程共享的区域，另外三个则是线程共享的。

## 方法区
用于存放已经被虚拟机加载的类信息，包括常量和静态变量等。

## 堆
