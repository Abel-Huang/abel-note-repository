## 常用JVM参数设置

### Trace跟踪参数
#### 
`verbose:gc` 正式版   
`-XX:printGC`  非正式版  
打印简要GC日志

`-XX:+PrintGCDetails` 
打印GC详细日志  
```
Heap
 PSYoungGen      total 37888K, used 655K [0x00000000d6000000, 0x00000000d8a00000, 0x0000000100000000)
  eden space 32768K, 2% used [0x00000000d6000000,0x00000000d60a3d88,0x00000000d8000000)
  from space 5120K, 0% used [0x00000000d8000000,0x00000000d8000000,0x00000000d8500000)
  to   space 5120K, 0% used [0x00000000d8500000,0x00000000d8500000,0x00000000d8a00000)
 ParOldGen       total 86016K, used 1323K [0x0000000082000000, 0x0000000087400000, 0x00000000d6000000)
  object space 86016K, 1% used [0x0000000082000000,0x000000008214acf0,0x0000000087400000)
 Metaspace       used 5098K, capacity 5264K, committed 5504K, reserved 1056768K
  class space    used 594K, capacity 627K, committed 640K, reserved 1048576K
```

`-XX:+PrintGCTimeStamps` 
打印GC发生的时间戳

`-Xloggc:<localtion>/<logfile>`
将GC日志重定向到指定的位置

`-XX:+PrintHeapAtGC`
每一次GC后，都打印堆信息

`-XX:+TraceClassLoading`
监控类的加载

`-XX:PrintClassHistogram`
打印类的直方图

### 堆的分配参数
1. `-Xmx -Xms`  
指定最大堆和最小堆(绝对值，单位为M、G)
Java会尽可能将内存维持在最小堆。 
 
2. `-Xmn`
设置新生代的大小(绝对值，单位为M、G)

3. `-XX:NewRatio`
新生代和老年代的比值，如设置为4表示新生代:老年代=1:4， 即新生代占堆的1/5

4. `-XX:SurvivorRatio`
Survivor和Eden区的比，如设置为8，表示Survivor：Eden=1:8,两个Survivor各占年轻代的1/10，
Eden区占年轻代的8/10.

5. `-XX:+HeapDumpOnOutOfMemoryError`
出现OOM时保存堆信息快照到文件中  

6. `-XX:+HeapDumpPath`
出现OOM时保存堆信息快照到文件的路径

7. `-XX:OnOutOfMemoryError=<path>`
出现OOM时会执行指定位置的脚本

8. `-XX：PermSize -XX:MaxPermSize`
设置永久代的初始空间和最大空间，永久代表示可以容纳多少类型；
在使用 CGLIB 等库的时候，可能会产生大量的类，大量的类型可能会撑爆永久区导致OOM。
但是JDK1.8 后已经取消永久代，所以参数已经被废弃了。

9. `-Xss`
设置栈的大小，通常只有几百k；
决定了函数调用的深度，每个线程都有自己的栈空间。

#### 总结
* 根据实际情况调整新生代和Survivor区的大小
* 推荐新生代占堆的3/8
* 推荐Survivor占新生代的1/10
* 在OOM时。Dump堆信息，确保可以排查现场问题。

### 栈的分配参数
`-Xss`
分配栈的大小，一般是几百k，如果分配过大, 在很多线程时会导致占空间分配过多，导致OOM
减少局部变量的使用，可以减少栈中局部变量的分配，从而可以分配更多的栈帧。








