## 主从复制和高可用

### 主从复制
1. 单机的问题
* 单点故障
* 容量瓶颈
* QPS瓶颈
2. 什么是主从复制

3. 主从复制的作用
数据副本
扩展读性能

4. 总结
* 一个master可以有多个slave
* 一个salve只能有一个master
* 数据流向是单向的，master到slave

5. 实现方式
* slaveof命令(异步)，slaveof  no one 取消复制
* 配置文件，一般需要设置从节点只读


