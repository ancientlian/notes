# Ribbon负载均衡服务调用

## 概述

Spring Cloud Ribbon 是基于 Netflix Ribbon 实现的一套客户端，主要功能是提供客户端的软件负载均衡算法和服务调用。

**注：Ribbon虽然大规模运用，但目前也进入维护,基本上不准备更新了**

负载均衡：将用户的请求平摊的分配到多个服务上，常见的负载均衡软件有Nginx，LVS等。

**Ribbon本地负载均衡客户端与Nginx服务端负载均衡的区别：**

Nginx是服务器负载均衡，客户端所有请求交给 Nginx，然后由 Nginx 实现转发请求。

Ribbon是本地负载均衡，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到 JVM 本地，从而在本地实现 RPC 远程服务调用技术。

由上面的区别可以将负载均衡分为两种：

**集中式LB：**在服务的消费方和提供方之间使用独立的LB设施（Nginx）
**进程内LB：**将LB的逻辑继承到消费方，消费方从服务注册中心获知哪些提供方地址可用，然后自己进行选择（Ribbon）
Ribbon就是一个软负载均衡的客户端组件，就是负载均衡+RestTemplate调用，与Eureka结合的架构如下：

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ody5tb2tlc3BhY2UuY24vaW1hZ2VzLzIwMjAvMDcvMTEvOWFkNGU5YzMwOWFiMzkxYjM1MzYzOTZhNjA0NWM3MGIucG5n?x-oss-process=image/format,png)

### 二说RestTemplate的使用

#### getForObject方法/getForEntity方法

![image-20210407120715688](https://i.loli.net/2021/04/07/35zq8AEW1IUMSLd.png)

#### postForObject/postForEntity

![image-20210407120757645](https://i.loli.net/2021/04/07/LFk7CD3SmMTUdQG.png)

## Ribbon常用负载均衡算法

Riboon使用 IRule 接口，根据特定算法从所有服务中,选择一个服务。而I Rule 接口有7个实现类。每个实现类代表一个负载均衡算法：

- com.netflix.loadbalancer.RoundRobinRule：轮询
- com.netflix.loadbalancer.RandomRule：随机
- com.netflix.loadbalancer.RetryRule：先轮询，失败则在指定时间内进行重试
- WeightedResponseTimeRule：对轮询的扩展，响应速度越快权重越大
- BestAvailableRule：先过滤处于断路状态的服务，选择一个并发量最小的服务
- AvailabilityFilteringRule：先过滤故障实例，再选择并发较小的实例
- ZoneAvoidanceRule：默认规则，根据Server所在区域的性能和可用性选择服务器。

### 如何替换算法

==官方文档明确给出了警告:
这个自定义配置类不能放在@ComponentScan所扫描的当前包下以及子包下，
否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享,达不到特殊化定制的目的了。==

1、新建package

2、新建MySelfRule规则类

```java		
import com.netflix.loadbalancer.IRule;
import com.netflix.loadbalancer.RandomRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MySelfRule {

    @Bean
    public IRule myRule(){
        return new RandomRule();//定义为随机
    }
}
 
 
```

3、主启动类添加@RibbonClient

```JAVA
import com.atguigu.myrule.MySelfRule;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.ribbon.RibbonClient;


@EnableEurekaClient
@SpringBootApplication
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration = MySelfRule.class)
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class,args);
    }

}
```



4、测试

http://localhost/consumer/payment/get/31



### 默认轮循负载均衡算法原理

负载均衡算法:  rest接口第几次请求数 % 服务器集群总数量=实际调用服务器位置下标，每次服务重启动后rest接口计数从1开始。

#### 源码分析

CAS+自旋锁

#### 手写

1、给pay模块(8001,8002)的controller方法添加一个方法,返回当前节点端口，用于测试自定义算法使用

```java
@GetMapping("/lb")
public String getPaymentLB(){
    return serverPort;
}
```


2、使用自己的算法需要修改order模块，去掉ApplicationContextConfig中的 @LoadBalanced 注解，以及Main上的@RibbonClient注解

3、自定义接口

```java
public interface LoadBalancer {
    //从服务列表中选取一个服务
    ServiceInstance instances(List<ServiceInstance> serviceInstances);
}
```


4、接口实现类

```java
@Component
public class MyLB implements LoadBalancer {

    private AtomicInteger atomicInteger = new AtomicInteger(0);

    private final int getAndIncrement(){
        int current;
        int next;
        do{
            current = this.atomicInteger.get();
            next = current >= Integer.MAX_VALUE ? 0 : current+1;
        }while(!this.atomicInteger.compareAndSet(current,next));
        System.out.println("****next: "+next);
        return next;
    }

    @Override
    public ServiceInstance instances(List<ServiceInstance> serviceInstances) {
        int index = getAndIncrement() % serviceInstances.size();
        return serviceInstances.get(index);
    }
}
```
5、修改80的controller:

添加两段代码：

```java
@Resource
private LoadBalancer loadBalancer;//自定义的
@Resource
private DiscoveryClient discoveryClient;
```
```java
@GetMapping("/payment/lb")
public String getPaymentLB(){
    List<ServiceInstance> serviceInstances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
    if(serviceInstances == null || serviceInstances.size() <= 0){
        return null;
    }
    ServiceInstance instance = loadBalancer.instances(serviceInstances);
    URI url = instance.getUri();
    return restTemplate.getForObject(url+"/payment/lb",String.class);
}
```


6、测试







# OpenFeign服务接口调用

## 概念

**Feign能干什么**

Feign旨在使编写Java Http客户端变得更容易。

前面在使用Ribbon+ RestTemplate时,利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处,==往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用==。所以，Feign在此基础上做了进一步封装， 由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下， ==我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口上面标注Mapper注解现在是一个微服务接口。上面标注一个Feign注解即可)==，即可完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时，自动封装服务调用客户端的开发量。

**Feign集成了Ribbon**

利用Ribbon维护了Payment的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与Ribbon不同的是,==通过feign只需要定义服务绑定接口且以声明式的方法==，优雅而简单的实现了服务调用

**Feign和OpenFeign两者区别**

<img src="https://i.loli.net/2021/04/07/QqNijlv1ZSoCO4E.png" alt="image-20210407172039008" style="zoom:200%;" />





## 使用步骤



2、修改pom

3、修改yml

4、添加主启动类

5、fegin需要调用的其他的服务的接口，建立service的接口

```java
package com.example.springcloud.service;

import com.example.springcloud.entities.CommonResult;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Component
@FeignClient(value = "CLOUD-PAYMENT-SERVICE")
public interface PaymentFeignService  {

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id);
}
```



6、编写控制层Controller

```java
package com.example.springcloud.controller;

import com.example.springcloud.entities.CommonResult;
import com.example.springcloud.entities.Payment;
import com.example.springcloud.service.PaymentFeignService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderFeginController {

    @Resource
    private PaymentFeignService paymentFeignService;

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPyamentById(@PathVariable("id")Long id){
        return paymentFeignService.getPaymentById(id);
    }
}
```



## 超时控制

==默认Feign客户端只等待一秒钟==，但是服务端处理需要超过1秒钟，导致Feign客户端不想等待了 ,直接返回报错。为了避免这样的情况，有时候我们需要设置Feign客户端的超时控制。

如果想要自定义OpenFeign客户端超时控制需要在YML文件里面开启

```YAML
#设置feign客户端超时时间(openFeign默认支持ribbon)
ribbon:
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下两端连接所用的时间
  connectTimeout: 5000
```



## 日志打印功能

Feign提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解Feign中Http请求的细节。说白了就是对Feign接口的调用情况进行监控和输出

- **NONE:**默认的,不显示任何日志;

- **BASIC:**仅记录请求方法、URL、响应状态码及执行时间;

- **HEADERS:**除了BASIC 中定义的信息之外,还有请求和响应的头信息; 

- **FULL:**除了HEADERS中定义的信息之外，还有请求和响应的正文及元数据。

#### 配置步骤

1、配置日志bean

FeignConfig.java

```java
import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {

    /**
     * 开启日志
     * - **NONE:**默认的,不显示任何日志;
     * - **BASIC:**仅记录请求方法、URL、响应状态码及执行时间;
     * - **HEADERS:**除了BASIC 中定义的信息之外,还有请求和响应的头信息;
     * - **FULL:**除了HEADERS中定义的信息之外，还有请求和响应的正文及元数据。
     * */
    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
```

2、YML文件里需要开启日志的Feign客户端

```yaml
logging:
  level:
    # feign 日志以什么级别监控哪个接口
    com.example.springcloud.service.PaymentFeignService: debug
```