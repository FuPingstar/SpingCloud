## 为什么要使用消息队列

### 同步变异步

![同步变异步-普通](/消息队列/RabbitMQ/images/同步变异步-普通.png)

![同步变异步-线程池](/消息队列/RabbitMQ/images/同步变异步-线程池.png)

![同步变异步-消息队列](/消息队列/RabbitMQ/images/同步变异步-消息队列.png)

### 解耦

![同步变异步-消息队列](/消息队列/RabbitMQ/images/同步变异步-消息队列.png)

### 流量削峰

![流量削峰.png](/消息队列/RabbitMQ/images/流量削峰.png)

## 原理

![原理图](/消息队列/RabbitMQ/images/原理.jpg)

### 消息（message）

消息是不具名的，由消息头和消息体组成，__消息头由一系列可选属性组成，包括：routing key(路由键)，priority(相对于其他消息的权重)，delivery mode(是否持久性存储)。__

### 生产者 （publisher）

向交换器发布消息的客户端应用程序。

### 消费者 (Consumer)

表示从消息队列中取得消息的客户端应用程序。

### 交换器 (Exchange)

用来接收生产者发送的消息并将这些消息路由给服务器中的队列

三种常用的交换器类型：

+ direct (发布与订阅，完全匹配)
+ fanout(广播)
+ topic (主题，规则匹配)

### 绑定（Binding）

绑定是基于__路由键__将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。

### 队列 (Queue)

用来保存消息的容器。也是消息的终点，一个消息可投入一个或多个队列，消息一直在队列里面，等待消费者链接到这个队列将其取走。

### 路由键 （RoutingKey）

`RabbitMQ` 决定消息将投递给哪个队列的规则

队列通过路由键绑定到交换器。

### 链接 （Connection）

Rabbit服务器和服务建立的TCP链接

### 信道 （Channel）

是TCP里面的虚拟链接

TCP一旦打开，就会创建`AMQP`信道

无论是发布消息，接收消息，订阅队列，这些操作都是信道完成的

### 虚拟主机 （Virtual Host）

每个 virtual host本质上是一个mini版的Rabbit服务器，拥有自己的队列，交换器，绑定，和权限机制，virtualhost是AMQP概念的基础，必须在创建连接的时候指定。Rabbit默认的

### Borker

一个消息队列服务器实体



### 理解

#### 消息队列和交换器的关系

交换器是由路由键和队列绑定在一起的，如果消息拥有的路由键跟交换器和队列的路由键相匹配，那么消息就会被路由到该绑定的队列中。

消息到队列的过程中，消息首先会经过交换器，接下来交换器在通过路由键匹配分发消息到具体的队列中

路由键可以理解为匹配的规则

#### Rabbit MQ为什么使用信道？而不是用TCP直接通信

TCP创建和销毁开销巨大，需要三次握手，四次释放

如果不用信道，而是使用TCP连接Rabbit，高峰时没秒成千上万的链接会造成资源的浪费，必定造成性能瓶颈。

信道的原理是一条信道一条通道，多条线程多条通道通用一个TCP连接，一个TCP连接可以容纳无限通道，即使每秒成千上万的请求也不会造成资源浪费。







## 简单队列

## 工作队列

### 轮询分发 `Round Robin`

### 公平分发 `Fair Dispatch`

## 消息应答

## 消息持久化

```java
	/*
	 * 声明一个队列
	 * @param queue 队列的名称
	 * @param durable 表示是否消息持久化 true表示持久化
	 */
    Queue.DeclareOk queueDeclare(String queue, boolean durable, boolean exclusive, boolean autoDelete,
                                 Map<String, Object> arguments) throws IOException;

```

> `RabbitQueue`不允许重新定义一个(参数不同的)已存在的队列。

## 交换器模式

### 发布订阅模式

### 广播模式

### 主题模式

## 消息确认机制

### 事务机制

AMQP实现了事务机制

### confirm串行

### confirm异步