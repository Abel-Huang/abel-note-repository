# NIO 
## 为什么 BIO 会慢？
jdk1.4 之前的 bio 是基于字节的，每次读写字节都需要进行校验，会耗费大量的时间在非 IO 传输的工作上，
所以 BIO 会比较慢。并且这种模式和操作系统底层的网络模型并不一致，所以带来了额外的性能损耗。

## NIO 总览
* Buffer 缓冲区：用于数据处理的基础单元，客户端发送与接收数据都需要通过 Buffer 进行转发，为 NIO 块状操作提供基础，数据都按块进行传输。
* Channel 通道：类似于流，但是不同于 In/Out Stream, 流具有独占性与单向性，通道则偏向于数据
的流通多样性，可以并发可异步读写数据。
* Selectors 选择器：处理客户端所有事件的分发器。
* Charset 字符编码：加密，解密，原生支持的，数据通道级别的数据处理方式，可以用于数据传输级别的数据加密、解密等操作。

### Buffer
ByteBuffer，CharBuffer，IntBuffer

### Channel
FileChannel，SocketChannel， ServerSocketChannel

### Selector
#### Selector 注册事件
* SelectorKey.OP_CONNECT 连接就绪
* SelectorKey.OP_ACCEPT 接受就绪
* SelectorKey.OP_READ 读就绪
* SelectorKey.OP_WRITE 写就绪

#### Selector 使用流程
* open() 开启一个选择器，可以给选择器注册需要关注的事件；
* register() 将一个 Channel 注册到选择器，当选择器触发对应关注事件时回调到 Channel 中，处理相关数据；
* select()/selectNow() 一个通道 Channel，处理一个当前的可用待处理的通道数据，返回的是 selectedKeys；
* selectedKeys 当前就绪的通道；
* wakeUp() 唤醒一个处于 select 状态的选择器；
* close() 关闭一个选择器，注销所有关注的事件;
* 注册到选择器的通道必须为非阻塞状态;
* FileChannel 不能用于 Selector，因为 FileChannel 不能切换为非阻塞模式，套接字通道就可以。