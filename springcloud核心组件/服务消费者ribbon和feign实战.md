### 服务消费者ribbon和feign实战

#### 常用的服务间调用方式讲解
1. RPC
- 远程过程调用，像调用本地服务一样调用服务器的服务。
- 支持同步、异步调用。
- 客户端和服务器之间建立TCP连接，可以一次建立一个，也可以多个调用复用一次连接。
- RPC数据包小

2. Rest(http)
- http请求，支持多种协议和功能。
- 开发方便成本低。
- http数据包大

#### 微服务调用方式之ribbon实战 订单调用商品服务

1. 创建order_service项目
- 依赖
```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>
```
2. 开发伪下单接口
3. 使用ribbon（类似于HttpServlet，URLConnection）
```
启动类增加注解
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        
        return new RestTemplate();
    }
```
4. 根据名称进行调用商品，获取商品详情

#### Ribbon负载均衡

__ribbon服务间调用负载均衡源码分析__
+ 完善下单接口

+ 分析@LoadBalanced
1. 首先从注册中心获取provider的列表。
2. 通过一定的策略选择其中一个节点
3. 再返回给RestTemplate

__LoadBalanced注解的作用是用LoadBalancedClient包装RestTemplate__
```
public interface LoadBalancerClient extends ServiceInstanceChooser {
    <T> T execute(String serviceId, LoadBalancerRequest<T> request) throws IOException;

    <T> T execute(String serviceId, ServiceInstance serviceInstance, LoadBalancerRequest<T> request) throws IOException;

    URI reconstructURI(ServiceInstance instance, URI original);
}
```
__LoadBalancedClient实现类__
```
public class RibbonLoadBalancerClient implements LoadBalancerClient {
```
__选择服务实例__
```
    public ServiceInstance choose(String serviceId, Object hint) {
        Server server = this.getServer(this.getLoadBalancer(serviceId), hint);
        return server == null ? null : new RibbonLoadBalancerClient.RibbonServer(serviceId, server, this.isSecure(server, serviceId), this.serverIntrospector(serviceId).getMetadata(server));
    }
```
```
   protected Server getServer(ILoadBalancer loadBalancer, Object hint) {
        return loadBalancer == null ? null : loadBalancer.chooseServer(hint != null ? hint : "default");
    }
```
__IloadBalance：负载均衡策略__
```
public interface ILoadBalancer {
    void addServers(List<Server> var1);

    Server chooseServer(Object var1);

    void markServerDown(Server var1);

    /** @deprecated */
    @Deprecated
    List<Server> getServerList(boolean var1);

    List<Server> getReachableServers();

    List<Server> getAllServers();
}
```
__ILoadBalance实现类BaseLoadBalance:获取服务列表__
```
负载均衡策略默认是：RoundRobinRule

private static final IRule DEFAULT_RULE = new RoundRobinRule();

 public List<Server> getAllServers() {
        return Collections.unmodifiableList(this.allServerList);
}
```
__负载均衡策略__
```
public interface IRule {
    Server choose(Object var1);

    void setLoadBalancer(ILoadBalancer var1);

    ILoadBalancer getLoadBalancer();
}
```

![负载均衡策略截图](/springcloud核心组件/images/负载均衡策略截图.png)

#### 服务间调用之负载均衡策略调整实战

__实战调整默认负载均衡策略实战__
+ 更改负载均衡策略 
```
官方文档
users:
  ribbon:
    NIWSServerListClassName: com.netflix.loadbalancer.ConfigurationBasedServerList
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.WeightedResponseTimeRule

项目配置
#自定义负载均衡策略
product-service:
  ribbon:
     NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule 策略的全路径    
```
####feign
+ 官方文档
```
23. Declarative REST Client: Feign
Feign is a declarative web service client. It makes writing web service clients easier. To use Feign create an interface and annotate it. It has pluggable annotation support including Feign annotations and JAX-RS annotations. Feign also supports pluggable encoders and decoders. Spring Cloud adds support for Spring MVC annotations and for using the same HttpMessageConverters used by default in Spring Web. Spring Cloud integrates Ribbon and Eureka to provide a load balanced http client when using Feign.

自己翻译：
声明式的Rest的客户端
Feign是声明式的web服务客户端。是的编写web服务客户端变得更容易。创建一个接口并且加上注解就可以使用Fegion。它包括Feign注解和JAX—RS注解的可插拔的注解支持。Fegin提供了可插拔的编译器和解码器。springcloud对其进行扩展，当在springweb中使用Feign的时候默认支持springmvc的支持并且使用相同的HttpMessageConverters。SpringCloud为Feign内置了Ribbon和Eureka使得使用它的时候提供http的负载均衡客户端。
```

#### 使用fegion步骤详解
+ 加入依赖
```
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```
+ 启动类增加注解
```
@EnableFeignClients
```
+ 增加一个接口
```
@FeignClient(name = "product-service")
public interface ProductClient {
    @GetMapping("/api/v1/product/find")
    String findById(@RequestParam(value = "id")int id);
}
```
+ 编码实战
```
    @Autowired
    private ProductClient productClie

            String response = productClient.findById(productid);
        JsonNode jsonNode = JsonUtil.str3JsonNode(response);
```
+ 注意点
1. 路径
2. Http方法必须对应
3. 使用RequestBody，应该使用POSTMAPPING
4. 多个参数的时候，通过@RequestParam("id")方式调用

#### 复写fegin默认配置
```
官方文档
23.2 Overriding Feign Defaults
A central concept in Spring Cloud’s Feign support is that of the named client. Each feign client is part of an ensemble of components that work together to contact a remote server on demand, and the ensemble has a name that you give it as an application developer using the @FeignClient annotation. Spring Cloud creates a new ensemble as an ApplicationContext on demand for each named client using FeignClientsConfiguration. This contains (amongst other things) an feign.Decoder, a feign.Encoder, and a feign.Contract. It is possible to override the name of that ensemble by using the contextId attribute of the @FeignClient annotation.

Spring Cloud lets you take full control of the feign client by declaring additional configuration (on top of the FeignClientsConfiguration) using @FeignClient. 

自己翻译
SpringCloud的Fegin支持的一个中心概念是命名客户端。每个Fegin客户端是整体的一部分，他们一起工作按需连接远程服务，整体有一个名称，开发人员可以使用@FeignClient注解为其命名。Springcloud根据需求使用FeignClientsConfiguration为每个命名的客户端创建一个新的整体作为一个ApplicationContext，这包含（其他)feign.Decoder feign.Encoder feign.Contract.为使用@FeignClient annotation的contextId属性的整体重命名是可能的。

使用注解@FeignClient，通过在FeignClientsConfiguration上面声明额外配置，springCloud让我们完全控制Fegin客户端。

```

#### Feign核心源码解读和服务调用方式ribbon和Feign选择

+ ribbon和feign两个区别和选择
1. 选择Feign
- 默认集成了ribbon
- 写起来更加思路清晰和方便
- 采用注解方式进行配置，配置熔断等方式方便

