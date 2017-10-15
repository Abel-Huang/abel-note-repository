# Java Exception
Java的异常机制可以保证程序在出现异常后还可以继续进行处理，而不是立即闪退。

## 处理异常的基本格式
```
  try {
            result = i/j;
            System.out.println(result);
        }catch (ArithmeticException e){
            System.err.println("exception: " + e.toString());
        }finally {
            System.out.println("finally");
        }
	}
```
try语句中是要捕获异常的部分的代码，catch是对异常进行处理的代码，finally表明无论是否出现异常都会执行的代码，可以作为程序的一个出口，在此语句块中尽量不要出现throw或者return语句，finally是可以被省略的。在执行完整个try catch语句之后才会执行下面的程序。

### 异常的处理机制
1. 产生异常，首先会生成一个异常类的实例化对象(可以由虚拟机产生，也可以由throw人为抛出)。
2. 在try语句中对异常对象进行捕捉；
3. 在catch语句中进行匹配，如果匹配成功就会执行catch中的语句。

需要注意的一点是，在使用时可以使用多级exception进行捕获，也可以使用Exception将所有的Exception一次全部捕获完，但是在使用过Exception之后不能在使用它的子类进行捕获，否则编译会错，has already been caught。(其实直接使用Throwable也是可以的，但是没有意义)

## 异常类的继承结构
Java异常主要分为两类Exception和Error，他们都是继承自Throwable类(这个类竟然不是接口或者抽象类)。
### Error
一般是JVM错误，包括JVM的内部错误和资源耗尽错误（例如OOM）。这种错误应该尽量避免，出错了也无法进行阻止，只能让程序进行关闭。
### Exception
一般是指程序开发的问题，可以通过try...catch语句进行避免。一般又分为RuntimeException和其他的Exception。
#### RuntimeException
RunTimeException继承自Exception类，和Error一样都是非检查性异常，可以不使用try...catch进行处理，直接将异常抛给JVM处理，当然这样程序也会停止。RuntimeException常见的子类如下：
* NullPointerException - 空指针引用异常
* ClassCastException - 类型强制转换异常。
* IllegalArgumentException - 传递非法参数异常。
* ArithmeticException - 算术运算异常
* ArrayStoreException - 向数组中存放与声明类型不兼容对象异常
* IndexOutOfBoundsException - 下标越界异常
* NegativeArraySizeException - 创建一个大小为负数的数组错误异常
* NumberFormatException - 数字格式异常
* SecurityException - 安全异常
* UnsupportedOperationException - 不支持的操作异常

#### 其他异常
直接继承自Exception的异常类，必须显式的用try...catch语句进行异常处理，否则编译就会不通过。

### throws和throw
* throws表示当前不处理异常，让调用者进行处理。
* throw表示认为的抛出一个异常，抛出时直接抛出异常类的实例化对象即可。

## 自定义异常类
