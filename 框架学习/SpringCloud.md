## 单体架构和分布式架构对比

单体架构的优点：容易测试，容易部署

单体架构的缺点：

- 开发效率低
- 代码维护难
- 部署不灵活（指的是构建时间特别长，有任何的代码修改都需要重新构建）
- 稳定性不高（牵一发动全身）
- 扩展性不足 

分布式架构:

- 服务注册:在服务治理框架中,需要有一个注册中心,每个服务单元向注册中心登记自己提供的服务,将主机与端口号、通信协议等一些附加信息告知注册中心。
- 服务发现：服务间的调用通过向服务名发起请求调用实现，服务调用方在调用服务提供方接口时并不知道具体的服务实例位置。

分布式系统的CAP定理：C数据一致性 A服务可用性 P服务对网络的容错性 这三个特性在任何分布式系统中不能被同时满足，最多两个

服务治理的三个核心要素：

- 服务注册中心：Eureka提供的服务端
- 服务提供者：提供服务的应用
- 服务消费者：消费者应用从服务注册中心获取服务列表，从而使消费者可以知道去何处调用服务

很多时候，客户端既是服务提供者也是服务消费者

## Eureka

- 作为服务发现和注册中心
- 遵循AP原则，即高可用、容错性，放弃数据一致性
- 使用RESTj进行服务调用而不是RPC
- 服务发现的两种方式:
  - 客户端发现:Eureka ,客户端从注册中心拿到可用服务列表在进行负载均衡。
  - 服务端发现:Nginx、Zookeeper、Kubernetes,服务端使用代理，使用负载均衡算法选择一个服务暴露给客户端
- 使用Ribbon对Eureka的服务发现进行负载均衡

### 高可用

Eureka的高可用可以通过搭建Eureka集群来实现：
通过互相之间进行注册以保证高可用

### 原理

#### 服务提供者

- 服务提供方启动的时候会发送REST请求的方式将自己注册到EurekaServer上，同时带上了自身服务的一些元数据，Server接收到这个REST请求之后，将元数据信息存储在一个双层的Mao结构上。其中第一层Key是服务名，第二层是具体服务的实例名。

- 两个服务提供方注册到两个不一样的服务注册中心上。它们的信息由两个服务注册中心所维护，当服务提供者发送注册请求到一个注册中心时，会被转发給集群中其他相连的注册中心。做到两个服务提供者的服务信息可以通过两台服务注册中心中的任意一台获得

- 服务提供者会维护一个心跳用于持续告知EurekaServer自己的服务仍然可用，防止EurekaServer剔除服务。此操作成为服务续约（Renew）

  ```
  eureka:
    client:
      service-url:
        defaultZone: http://localhost:8761/eureka
    instance:
    	//服务续约调用的间隔时间
      lease-expiration-duration-in-seconds: 30
      //定义服务失效的时间
      lease-renewal-interval-in-seconds: 90
  ```

  

## 微服务拆分

### 起点和重点

- 起点：既有的架构形态
- 终点：好的架构不是设计出来的，而是进化而来的。一直在演进

###不适合微服务的业务场景

- 系统中包含很多很强的事物场景
- 业务相对稳定，迭代周期长
- 访问压力不大，可用性要求不高

#### 拆分的方法论

##### 扩展立方模型：

- X轴：水平复制，通过副本扩展，通过负载均衡运行多个完全一样的程序
- Z轴：数据分区，每个服务器负责一个数据子集，每个服务器运行的代码是一样的
- Y轴：功能解耦，将不同职责的模块分成不同的服务。

##### 如何拆分“功能”

###### 单一职责、松耦合、高内聚

- 单一职责：每个服务只负责业务功能的一个单独的部分
- 松耦合：服务之间耦合度低，修改一个服务其他服务不需要修改
- 高内聚：服务内部相关的行为都聚集在一个服务内，要修改一个服务时只需要修改一个服务即可

###### 关注点分离

- 按职责：给我们的服务进行分类，比如订单，商品，app等
- 按通用性：一些基础组件与具体的业务无关的也可以单独拿出来，比如消息服务，用户服务等
- 按粒度级别：订单服务后期可能要拆分成订单服务和支付服务两个服务

###### 服务和数据的关系

- 先考虑业务功能，在考虑数据
- 无状态服务：如果一个数据需要被多个服务共享才能完成一个请求，那么这个数据就可以成为状态，微服务架构中需要将有状态的业务服务改变成的无状态服务，比如session

## 服务消费

### Ribbon

Ribbon是一个基于HTTP和TCP的客户端负载均衡器，通过客户端中配置的ribbonServerList服务端列表去轮询访问以达到均衡负载的作用。当Ribbon与Eureka联合使用时，Ribbon的服务清单会被DIscoveryEnabledNIWSServerList重写，扩展为从Eureka中获取服务端列表。

负载均衡也分为客户端负载均衡和服务器端负载均衡两种，而Ribbon则明显是客户端负载均衡。



在SpringCloudRibbon的封装之后，微服务架构使用客户端负载均衡只需要两步：

- 服务提供方只需要启动多个服务实例并注册到注册中心或是多个相关联的服务注册中心
- 服务消费者直接通过调用被`@LoadBalanced`注解修饰过的RestTemplate来实现服务的接口调用

### RestTemplate + Ribbon

先写一个配置类

```
@Component
public class RestTemplateConfig {
    @Bean
    @LoadBalanced //负载均衡
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

然后直接可以通过编写应用的名称来调用服务

```
//第三种方式（）
String response = restTemplate.getForObject("http://PRODUCT/msg", String.class);
log.info("response={}", response);
return response;
```

### Feign

- 声明式REST客户端(伪装RPC)
- 采用了基于接口的注解

用法:

- 添加依赖

- 添加注解启动 `@EnableFeignClients(basePackages = "com.gdou.product.client")` ,记得需要定义要扫描的包

- 定义调用的接口:

  ```java
  @FeignClient(name = "product")//访问product应用下面的msg接口
  public interface ProductClient {
      @GetMapping("/msg")
      String productMsg();
  }
  ```

- 调用接口:

  ```java
  private ProductClient productClient;
    @GetMapping("/getProductMsg")
      public String getProductMsg() {
          String response= productClient.productMsg();
          //通过feign
          log.info("response={}", response);
          return response;
      }
  ```

- 



## 消息队列:RabbitMQ

- 使用SpringCloud Stream对消息队列进行操作
  - 在中间件(消息队列)与应用之间加了一层Binder进行交互,做到代码对中间件的无感知
  - 
- 使用ASQP的API进行操作
  - 
- MQ的使用场景:
  - 异步处理:例如用户注册之后给用户发短信,用户消费进行积分增加
  - 流量削峰:高并发秒杀
  - 日志处理:Kafka
  - 应用解耦:用户下单后订单通知系统
- 使用MQ的时候需要考虑到MQ的消息一致性以及出现异常之后的回滚问题?
- 可靠的消息投递
- 用户体验的变化
- 根据实际情况来
- 异步的常见形态:
  - 通知
  - 请求/异步响应
  - 消息:(一对多/消费订阅)
  - 使用异步需要考虑到出异常之后的事务回滚问题,其中要考虑到Redis和mq的回滚以及异常处理,例如使用trycatch来进行Redis的手动回滚,例如将MQ的发送统一整理到另外一个方法.

## 配置中心Config

- 从配置中心拉取配置文件,并实现配置中心的配置拉取

- 高可用:多启动几台实例即可;当启用多台config的时候,会使用到Eureka的负载均衡,轮询访问多台config

- 配置中心会进行合并,例如order.yml回合order-test.yml进行合并

- springCloudBus

  - @RefreshScope 注释在需要用到更新的地方 这样才能自动更新

  

## 服务网关Zuul

- 实现路由功能

使用nginx进行请求负载均衡

```yml
自定义路由：
zuul:
  host:
    connect-timeout-millis: 15000 #HTTP连接超时要比Hystrix的大
    socket-timeout-millis: 60000   #socket超时
  routes:
    myProduct:
      path: /myProduct/**
      serviceId: product
```

查看所有的路由表：

```yml
management:
  endpoints:
    web:
      exposure:
        include: "*"
  endpoint:
    health:
      show-details: ALWAYS
```

加上以上的配置

- 配置路由过滤器



### Zuul的高可用

将多个Zuul节点注册到EurekaServer上

Nginx和Zuul进行混搭



### 限流

令牌桶限流

## 服务容错保护 Hystrix

​	微服务架构中，存在很多服务单元，若一个单元出现故障，很容易导致因依赖关系导致的故障蔓延。最终导致整个系统的瘫痪，断路器模式解决了缓解了这个问题:当某个服务单元发生了故障，通过断路器的故障监控，向调用方返回一个错误响应而非长时间的等待，以避免故障在分布式系统中的蔓延。

​	Hystrix实现了断路器，线程隔离、服务降级、服务熔断、请求缓存、请求合并和服务监控等一系列的服务保护功能。



服务降级：

优先核心服务，非核心服务不可用或者弱可用

通过



依赖隔离：



服务熔断：

当服务请求故障达到一定比例的时候，启动熔断机制，对请求都返回失败。

## 服务跟踪 SpringCloudSleuth



## 开发错误

- 配置datasource的driver-class-name问题:

  在