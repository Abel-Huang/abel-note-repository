# JVM 的内存分区

JVM 运行时内存主要分成五个部分，分别是：
1. 方法区，Method Area
2. 堆，Heap
3. 虚拟机栈，VM Stack
4. 本地方法栈， Native Method Stack
5. 程序计数器寄存器， Program Counter Register

其中方法区和堆是线程共享的区域，另外三个则是线程独占的。

## PC 寄存器
* 每个线程拥有一个PC 寄存器，用于指向下一条运行指令的地址；
* 在线程创建时创建；
* 执行本地方法时，PC的值为undefined。
 
## 方法区
* 用于存放已经被虚拟机加载的类信息，包括常量和静态变量等，是所有线程共享的。
* JDK7之前，字符串常量池被存储在永久代（默认大小是4m）中，因此导致性能问题和OOM；实际上1.7时，存储在永久代的部分数据已经转移到了Java Heap或者是 Native Heap；JDK8后，永久代被移到与一个堆不相连的本地内存区域，也即就是元空间(Meta Space)
由于类的元数据分配在本地内存中，元空间的最大可分配空间就是系统可用内存空间。

## 堆
* 堆内存是线程共享的，用于存放对象实例；
* 是GC的主要工作区域；
* GC一般是分代的。

## Java 栈
* 栈是线程私有的，存放的是一个个的Java栈帧；
* 帧保存的是一个方法的局部变量表(包含参数和局部变量)，操作数栈(Java虚拟机没有寄存器，所有的参数传递都依赖于操作数栈)和常量池指针等；
* 每次方法调用都会创建一个Java栈帧，并且进行压栈，方法调用完成会出栈。

## 本地方法栈
用于存放本地方法的信息。

## 具体实例
```java
public class ExampleTest{
    public static void main(String[] args){
      Example example = new Example();
      example.printName();
    }
}
public class Example{
    private String name;
    public Example(String name){
        this.name = name;
    }
    public void printName() {
        System.out.println(name);
    }
}
```
在上述代码中，example 的实例 new Example() 存放在堆中，
ExampleTest 的类型信息(包含 main 方法)，Example 的类型信息(包含 printName 方法)都存放在方法区，
执行 main() 方法的主线程调用栈和局部变量 example 引用都存放在 Java栈中。