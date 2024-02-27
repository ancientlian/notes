# Eureka服务注册与发现

## Eureka基础知识

### 什么是服务治理

在传统的rpc远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等,实现服务发现与注册。

### 什么是服务注册

### Eureka两组件

Eureka包含了两个组件：

Eureka Server：提供注册服务，各个微服务节点会在其中进行注册。

Eureka Client：一个Java客户端，用于简化与Eureka Server 的交互，同时会定时向Eureka Server发送心跳，如果Eureka Server 在多个心跳周期内没有接收到某个节点的心跳则会将该节点移除。

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ody5tb2tlc3BhY2UuY24vaW1hZ2VzLzIwMjAvMDcvMDcvMmI3Mzk1NzljNTg1ZjgzYTkyZTYyMmZkN2E0MzIzN2QucG5n?x-oss-process=image/format,png)

## 单机版Eureka构建步骤

### 生成服务注册中心

建module

修改pom文件

编写yml配置文件

写主启动类

测试：http://localhost:7001

### 8001注册到eureka，成为服务提供者provider

修改pom文件

写入yaml配置

增加主启动注解

测试

```yaml
spring:
  application:
    name: cloud-payment-service
```

这个名字就是入住的名字



### 80入住，注册进eurekaserver成为服务消费者consumer

方法同样





## 集群版eureka

### 集群原理

<img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ody5tb2tlc3BhY2UuY24vaW1hZ2VzLzIwMjAvMDcvMDcvOGM0YzAxMGIxMTM0ZTY0MzVkMWRhNjUxNjhiYzI3YjYucG5n?x-oss-process=image/format,png" alt="在这里插入图片描述" style="zoom:150%;" />

- pay模块与order模块启动时，会注册自己并将自身信息也放入EurekaServer
- 当order模块调用pay模块时，会先从EurekaServer拿到pay模块的调用地址
- 底层通过HttpClient调用并且还会缓存一份到本地，每30秒更新一次
- 如果注册中心只有一个，出现故障就会导致整个微服务环境不可用



### 构建7002，相互注册相互守望

1、新建项目cloud-eureka-server7002

2、主启动类、pom文件复制7001中的

3、修改映射配置文件

C:\Windows\System32\drivers\etc下的hosts文件

```
127.0.0.1 eureka7001.com
127.0.0.1 eureka7002.com
```

4、修改yml文件

7001

```yaml
server:
  port: 7001
eureka:
  instance:
    # eureka服务端的实例名称
    hostname: eureka7001.com  #eureka服务端的示例名称
  client:
    # 不注册自己
    register-with-eureka: false
    # 表示自己是注册中心，不用检索服务
    fetch-registry: false
    service-url:
      defaultZone: http://eureka7002.com:7002/eureka/
```

7002

```yaml
server:
  port: 7002
eureka:
  instance:
    # eureka服务端的实例名称
    hostname: eureka7002.com  #eureka服务端的示例名称
  client:
    # 不注册自己
    register-with-eureka: false
    # 表示自己是注册中心，不用检索服务
    fetch-registry: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

7002也是一样的,只不过端口和地址改一下，多台在defaultZone用逗号隔开

### 订单微服务注册到eureka集群

80

```yaml
server:
  port: 80

spring:
  application:
    name: cloud-order-service

eureka:
  client:
    # 不注册自己,表示是否将自己注册进EurekaServer，默认为true
    register-with-eureka: true
    # 表示自己是注册中心，不用检索服务
    # 是否从EurekaServer抓取已有的注册信息，默认为true。
    # 单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka , http://eureka7002.com:7002/eureka  # 集群版
```

8001

```yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    # 当前数据源操作类型
    type: com.alibaba.druid.pool.DruidDataSource
    # mysql驱动类
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/cloud?useUnicode=true&characterEncoding=UTF-8&useSSL=false
    username: root
    password: 123456

eureka:
  client:
    # 不注册自己
    register-with-eureka: true
    # 表示自己是注册中心，不用检索服务
    fetch-registry: true
    service-url:
      # defaultZone: http://localhost:7001/eureka  # 单机版
      defaultZone: http://eureka7001.com:7001/eureka , http://eureka7002.com:7002/eureka  # 集群版


mybatis:
  mapper-locations: classpath*:mapper/*.xml
  type-aliases-package: com.example.springcloud.entities
```

### 测试

先要启动EurekaServer，7001/7002服务

再要启动服务提供者provider,8001

再要启动消费者，80

http://localhost/consumer/payment/get/31



### 将pay模块也配置为集群模式（支付微服务集群配置）

1. 新建名称为cloud-provider-payment8002的Module
2. **主启动类**、pom文件、mapper、service、controller复制8001的
3. 配置文件复制8001的，端口修改一下,改为8002，服务名称不用改,用一样的
4. 修改8001/8002的controller

```java
@Value("${server.port}")
private String serverPort;

······
	return new CommonResult(200, "插入成功, serverPort = " + serverPort, result);
······
```

#### 负载均衡

目前所有的http://localhost/consumer/payment/get/12调用的都是8001端口，原因在于我们之前在82的controller里面写死了`private static final String PAYMENT_URL = "http://localhost:8001";`

1、对外暴露的是微服务名称，首先要添加注解，使RestTemplate开启负载均衡（可以指定负载均衡算法,默认轮询）

```JAVA	
@Configuration
public class ApplicationContextConfig {
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

2、修改80的OrderController

```JAVA
private static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";
```

### actuator信息完善

主机名称：服务名称修改

添加

```yaml
instance:
	instance-id: payment8002
```

完整位置

```yaml
eureka:
  client:
    # 不注册自己
    register-with-eureka: true
    # 表示自己是注册中心，不用检索服务
    fetch-registry: true
    service-url:
      # defaultZone: http://localhost:7001/eureka  # 单机版
      defaultZone: http://eureka7001.com:7001/eureka , http://eureka7002.com:7002/eureka  # 集群版
  instance:
    instance-id: payment8002
```



访问信息有ip信息提示

添加`prefer-ip-address: true`

完整：

```yaml
eureka:
  client:
    # 不注册自己
    register-with-eureka: true
    # 表示自己是注册中心，不用检索服务
    fetch-registry: true
    service-url:
      # defaultZone: http://localhost:7001/eureka  # 单机版
      defaultZone: http://eureka7001.com:7001/eureka , http://eureka7002.com:7002/eureka  # 集群版
  instance:
    instance-id: payment8001
    prefer-ip-address: true
```

效果

![image-20210406140814921](https://i.loli.net/2021/04/06/zTSNJn5XZMoBxeU.png)

### 服务发现Discovery

对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息

1、修改cloud-provider-payment8001的Controller

```java
@Resource
private DiscoveryClient discoveryClient;
······

/**服务发现Discovery*/
    @GetMapping(value = "/payment/discovery")
    public Object discovery(){
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            log.info("***** element:"+element);
        }
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
```

2、在主启动类里面添加注解`@EnableDiscoveryClient`

### Eureka自我保护

#### 故障现象

![image-20210406155049686](https://i.loli.net/2021/04/06/PEZhjkVsf6bINJ5.png)

#### 导致原因

##### 为什么产生Eureka自我保护机制?

为了防止EurekaClient可以正常运行,但是与EurekaServer网络不通情况下，EurekaServer==不会立刻==将EurekaClient服务 剔除

##### 什么是自我保护模式?

默认情况下，如果EurekaServer在一 定时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例(默认90秒)。但当网络分区故障发生(延时、卡顿、 拥挤)时,微服务与EurekaServer之间无法正常通信，以上行为可能变得非常危险了——因为微服务本身其实是健康的，==此时本不应该注销这个微服务==。

Eureka通过 ” 自我保护模式”来解决这个问题一当EurekaServer节点在短时间内丢失过多客户端时(可能发生了网络分区故障)， 那么这个节点就会进入自我保护模式。

![image-20210406155443738](https://i.loli.net/2021/04/06/Q4m36MEUrxl2acg.png)

==在自我保护模式中，Eureka Server会保护服务注册表中的信息,不再注销任何服务实例。==
它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。==一句话讲解:好死不如赖活着==

综上，自我保护模式是一种应对网络异常的安全保护措施。 它的架构哲学是宁可同时保留所有微服务(健康的微服务和不健康的微服务都会保留)也不盲目注销任何健康的微服务。

使用自我保护模式，可以让Eureka集群更加的健壮、稳定。

**一句话：某时刻某一个微服务不可用了，Eureka不会立刻清理，依旧会对该微服务的信息进行保存**

**属于CAP里面的AP分支**

#### 怎么禁止自我保护

一般生产环境中不会禁止自我保护

##### 注册中心eureakeServer端7001

```YAML
server:
  enable-self-preservation: false
  eviction-interval-timer-in-ms: 2000
```



##### 生产者客户端eureakeClient端8001

默认

```yaml
eureka.instance.lease-renewal-interval-in-seconds=30
eureka.instance.lease-expiration-duration-in-seconds=90
```

可以修改时间

```yaml
instance:
   lease-renewal-interval-in-seconds: 1
   lease-expiration-duration-in-seconds: 2
```



---





# Zookeeper服务注册与发现



# Consul服务注册与发现





# 三个注册中心异同点

## CAP

- C: Consistency(强一致性)
- A: Availability(可用性)
- P: Partition tolerance(分区容错)
- CAP理论关注粒度是数据，而不是整体系统设计的策略

==最多只能同时较好的满足两个。==

CAP理论的核心是:一个分布式系统不可能同时很好的满足一致性, 可用性和分区容错性这三个需求，因此，根据CAP原理将NoSQL数据库分成了满足CA原则、满足CP原则和满足AP原则三大类:

CA-单集群，满足一致性,可用性的系统，通常在可扩展性坏太强大。

CP -满足-致性，分区容忍必的系统，通常性能不是特别高。

AP -满足可用性,分区容忍性的系统，通常可能对一致性要求低一些。

![image-20210407105051905](https://i.loli.net/2021/04/07/qoBUICHVaMQz7u2.png)

**举例**

- AP(Eureka)

**AP架构**

当网络分区出现后，为了保证可用性,系统B==可以返回旧值==,保证系统的可用

==结论:违背了一致性C的要求，只满足可用性和分区容错,即AP==

![image-20210407114323795](https://i.loli.net/2021/04/07/deyVzG2KClAZjhF.png)

- CP(Zookeeper/Consul)

**CP架构**

当网络分区出现后，为了保证一致性, 就必须拒接请求，否则无法保证一致性

==结论:违背了可用性A的要求，只满足一致性和分区容错，即CP==

![image-20210407114523249](https://i.loli.net/2021/04/07/aSq9XxE82her3LF.png)