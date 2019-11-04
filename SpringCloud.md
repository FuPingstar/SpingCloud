#### 什么是SpringCloud

+ SpringCloud为开发人员提供快速构建分布式系统的一些通用模式，包括：配置管理，服务发现，服务短路，智能路由，微型网关，控制总线，一次性令牌，全局锁，领导选举，分布式会话和全局状态。

#### Bootstrap上下文

+ bootstrap上下文是Springcloud新引入的，与传统的Spring上下文相同，系ConfigurableApplicationContext实例，由BootstrapApplicationListener在监听ApplicationEnvironmentPreparedEvent时创建。

#### Spring事件/监听器模式

+ Spring的applicationContext能够发布事件和注册对应的事件监听器，有一套完整的事件发布和监听机制。

+ 三个概念

  + 事件源：事件的产生者，任何一个的事件都必须有一个事件源

    ```java
    看一下java中EventObject的构造方法
        public EventObject(Object source) {
            if (source == null)   // 每一个事件必须有一个事件源
                throw new IllegalArgumentException("null source");
    
            this.source = source;
        }
    ```

    

  + 事件广播器：事件和监听器之间的桥梁，负责把事件通知给事件监听器

  + 事件监听器注册表：spring框架为所有监听器提供了一个存放的地方

+ ApplicationEvent: 事件类   __该类是一个抽象类，继承java的EventObject__

  + 构造函数：ApplicationEvent(Object source) ，指定事件源
  + 两个子类
    + ApplicationContextEvent：容器事件，也就是说事件源是ApplicationContext，框架提供了四个子类，分别代表容器启动，刷新，停止，和关闭事件。
    + RequestHandlerEvent：与web应用相关的一个事件，当一个请求被处理后，才会产生该事件。

+ ApplicationListener：事件监听器接口

  + 所有的监听器都需要实现该接口，该接口只定义了一个方法：onApplicaitonEvent (Eevent)，该方法接收事件对象，在该方法中编写事件的响应处理逻辑。



#### SpringApplication

+ SpringApplication是SpringBoot引导启动类，与Springboot上下文，事件，监听器以及环境等组件关系紧密，其中提供了控制SpringBoot应用特征的行为方法。

#### Springboot应用运行监听器

+ SpringApplicationRunListener

#### SpringBoot事件

+ 事件触发器：EventPublishingRunListener
+ ApplicationEvent
+ ApplicationEnvironmentPreparedEvent
+ ApplicationPreparedEvent
+ ApplicationReadyEvent/ApplicationFailedEvent

#### Springboot cloud上下文层次关系

+ SpringBoot上下文(子上下文)
  + 非web应用  AnnotationConfigApplicationContext
  + web应用  AnnotationConfigEmbeddedWebApplicationContext
+ SpringCloud上下文
  + BootStrap(父上下文)
+ SpringCloud 上下文Bootstrap优先于SpringBoot上下文，并且SpringCloud上下文是Springboot上下文双亲。





#### Actuator EndPoints

+ Actuator在SpringBoot使用场景中表示为生产而准备的特性，这些特性通过http端口的形式，帮助相关人员管理和监控应用。
+ 监控类：”端点信息“，”应用信息“，”外部化配置信息“，”指标信息“，”健康检查“，”Bean管理“，”WebURL映射管理“，”Web URL 跟踪“
+ 管理类："外部化配置"，"日志配置"，"线程dump"，"堆dump"，"关闭应用"。
+ SpringCloud扩展了Actuator EndPoints
  + 上下文重启
  + 暂停
  + 恢复









