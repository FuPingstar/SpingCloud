## 为什么要使用消息队列

### 同步变异步

![同步变异步-普通](/消息队列/RabbitMQ/images/同步变异步-普通.png)

### 解耦

### 流量削峰

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

## 订阅模式

## 路由模式

## 主题模式

## 消息确认机制

### 事务机制

AMQP实现了事务机制

### confirm串行

### confirm异步

