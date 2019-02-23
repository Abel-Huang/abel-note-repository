# RabbitMQ 基础原理

## RabbitMQ 高性能的原因
1. 采用 Erlang 语言，在 Broker 之间进行数据交换的性能是非常优秀的；

## AMQP
AMQP 是一个上层的二进制应用层消息协议。
### 核心概念
1. Server：又称Broker，接受客户端连接，实现AMQP实体服务；
2. Channel：网络信道，几乎所有的操作都在 Channel 中进行，
Channel 是进行消息读写的通道，客户端可以建立多个 Channel，
每 Channel 代表一个会话任务，有点类似数据库操作中的会话 session；
3. Message：消息，服务器和应用程序之间传递的数据，RabbitMQ 
的消息是由 Properties 和 Body 组成，其中 Properties 对消息进行修饰，
类似于元信息，如消息的优先级、延迟等特性，body 就是消息体内容；
消息的结构是由 AMQP 协议确定的，不同的 mq 消息结构是不同的；
4. Virtual host: 虚拟地址，用于进行逻辑隔离，最上层的消息路由。
一个 Virtual Host 里面可以有若干个 Exchange 和 Queue, 同一个
Virtual Host 里面不能有相同名称的 Exchange 或 Queue；
5. Exchange：交换机，接收消息，根据路由键转发消息到绑定的队列；
6. Binding:  Exchange 和 Queue 之间的虚拟连接，binding 中可以
包含 routing key 路由键；
7. Routing key：一个路由规则，虚拟机可以用此来确定如何路由一个
特定消息；
8. Queue：消息队列，保存消息并将它们转发到消费者；

### RabbitMQ 工作流程
1. producer client 生产消息，并且将消息投递到 Exchange，需要注明
具体的 Exchange 和 routing key；
2. 到达 Exchange 的 Message 会根据 routing key 进行过滤，路由到不同的
Queue；
3. consumer 会监听指定的 queue，从中获取 message。

## RabbitMQ的使用


## Exchange 交换机
### 属性
1. Name： 交换机名称
2. Type：交换机类型 direct，topic，fanout，headers
    * direct：会被转发到 RoutingKey 中所指定的 Queue，
    要求 RoutingKey 是完全匹配的； 
    * topic：所有发送到 Topic Exchange 的消息被转发到所有关心 
    RouteKey 中指定 Topic 的 Queue 上，Exchange 将 RoutingKey
    和某个 Topic 进行模糊匹配，队列需要绑定一个 Topic；
    * fanout：不处理路由键，只需要简单的将队列绑定到交换机上，
    发送到交换机的消息都会被转发到与该交换机绑定的所有队列上，
    fanout 交换机转发消息是最快的(性能最好)；
    
3. Durability：是否需要持久化，true 为持久化
4. Auto Delete：当最后一个绑定到 Exchange 上的队列删除后，
自动删除该 Exchange
5. Internal：当前 Exchange 是否用于 RabbitMQ 内部使用，
默认为 False，一般就使用默认值

## 绑定 Binding
Exchange 和 Exchange，Queue 之间的连接关系。

## 消息队列 Queue
消息队列，实际存储消息数据。
### 属性
1. Durability 是否持久化
Durable：是，Transient：否
2. Auto delete：yes 表示监听移除后，Queue会被自动删除

## Message 消息
服务器和应用程序之间传送的数据，本质就是一段数据，由Properties
和Body 组成。
### 属性
1. delivery mode
2. headers
3. content_type
4. content_encoding
5. priority
6. correlation_id: 唯一id，在幂等、路由等会用到；
7. reply_to
8. expiration: 消息过期时间
9. message_id: 

## Virtual host 虚拟主机
1. 虚拟主机，用于进行逻辑隔离，最上层的消息路由；
2. 一个 Virtual Host 里面可以有若干个 Exchange 和 Queue；
3. 同一个 Virtual Host 里面不能有相同名称的 Exchange 或 Queue。