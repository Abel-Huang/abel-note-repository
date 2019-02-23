# JVM 规范
Java 虚拟机规范定义了以下内容：
* Class 文件格式
* 数字的内部表示和存储
* returnAddress 数据类型定义
* 定义 PC
* 堆
* 栈
* 方法区
* 虚拟机指令集
    * 类型转换 l2i
    * 出栈入栈操作 aload， astore
    * 运算 iadd， isub
    * 流程控制 ifeq，ifne
    * 函数调用 invokevirtual，invokeinterface，invokespecial，invokestatic
* JVM 还需要对 Java Library 提供以下支持，因为这些库无法直接在语言层面实现
    * 反射，java.lang.reflect
    * ClassLoader
    * 初始化class和interface
    * 安全相关，java.security
    * 多线程
    * 弱引用
* JVM 的编译
只要满足 JVM 规范的实现，都是 Java 虚拟机。

## 整数的表达
1. 原码：第一位为符号位(0为正数，1为负数)
2. 反码： 符号位不变，剩余的原码取反
3. 负数补码：符号位不动，反码加1
4. 正数补码：和原码相同
```
-6
原码：10000110
反码：11111001
补码：11111010

6
原码：00000110
反码：01111001
补码：00000110
```
使用补码的原因：
1. 合理的处理0；
2. 方便加减法得到统一处理。

## Float 的表示
IEEE754