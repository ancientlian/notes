# Hystrix断路器

## 概述

**分布式系统面临的问题**

==复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。==

**服务雪崩**：

多个微服务之间调用时，假设微服务 A 调用微服务 B 和微服务 C，微服务 B 和微服务 C 又调用其它的微服务，这就是所谓的”**扇出**“。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务 A 的调用就会占用越来越多的系统资源，进而导致系统”**雪崩效应**“。

对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。

所以，通常当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接收流量，然后这个有问题的模块还调用了其他的模块，这样就会发生**级联故障**，或者叫**雪崩**。

**Hystrix**

Hystrix是一个于处理分布式系统的**延迟**和**容错**的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下， ==不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。==
”断路器”本身是一种开关装置， 当某个服务单元发生故障之后，通过断路器的故障监控(类似熔断保险丝)，==向调用方返回一个符合预期的、可处理的备选响应(FallBack) ，而不是长时间的等待或者抛出调用方无法处理的异常==，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。





## 服务降级

### 创建带降级机制的pay模块

### 创建带降级的order模块









## 服务熔断

**熔断机制概述**
熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时,会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。
**当检测到该节点微服务调用响应正常后，==恢复调用链路==。**

当某个服务出现问题：

- 需要关闭所有对此服务的访问【断路器】，然后调用**服务降级**。
- 当检测到该服务响应正常后，**恢复调用链路**。

在Spring Cloud框架里,熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败,就会启动熔断机制。熔断机制的注解是@HystrixCommand。

论文：https://martinfowler.com/bliki/CircuitBreaker.html



### 测试步骤

1、PaymentService.java

```java
/**
 * 服务熔断测试
 */
@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
        @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),  //是否开启断路器
        @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),   //请求次数
        @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),  //时间范围
        @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"), //失败率达到多少后跳闸
})
public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
    if (id < 0) {
        throw new RuntimeException("*****id 不能负数");
    }
    String serialNumber = IdUtil.simpleUUID();

    return Thread.currentThread().getName() + "\t" + "调用成功,流水号：" + serialNumber;
}

public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
    return "id 不能负数，请稍候再试,(┬＿┬)/~~     id: " + id;
}
```



2、PaymentController.java

```java
/**
 * 服务熔断
 */
@GetMapping("/payment/circuit/{id}")
public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
    String result = paymentService.paymentCircuitBreaker(id);
    log.info("*******result:" + result);
    return result;
}
```



3、记得主函数添加`@EnableCircuitBreaker`标签

```java
@SpringBootApplication
@EnableEurekaClient
//服务降级
@EnableCircuitBreaker
public class PaymentHystrixMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentHystrixMain8001.class, args);
    }
}
```



4、测试

多次错误,然后慢慢正确，发现刚开始不满足条件，就算是正确的访问地址也不能进行访问，需要慢慢的恢



### 原理

熔断打开：请求不再进行调用当前服务，内部设置时钟一般为MTTR(平均故障处理时间)，当打开时长达到所设时钟则进入熔断状态

熔断关闭：熔断关闭不会对服务进行熔断

熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断

![image-20210409124758399](https://i.loli.net/2021/04/09/czRp1lwq2r8Eh9g.png)







断路器打开之后：

1、再有请求调用的时候,将不会调用主逻辑，而是直接调用降级fallback。 通过断路器,实现了自动地发现错误并将降级逻辑切换为主逻辑，减少响应延迟的效果。

2、原来的主逻辑要如何恢复呢?
对于这一问题，hystrix也为我们实现了 自动恢复功能。
当断路器打开，对主逻辑进行熔断之后, hystrix会启动一个休眠时间窗,在这个时间窗内,降级逻辑是临时的成为主逻辑,当休眠时间窗到期，断路器将进入半开状态,释放-次请求到原来的主逻辑上,如果此次请求正常返回,那么断路器将继续闭合,主逻辑恢复，如果这次请求依然有问题，断路器继续进入打开状态,休眠时间窗重新计时。





## 服务限流

alibaba的Sentinel



## 服务监控

### HystrixDashboard

#### 平台搭建

1. 创建项目
2. pom文件
3. yml文件
4. 主启动类
5. 所有的pay模块(8001,8002,8003…)添加一个pom依赖`spring-boot-starter-actuator`
6. 启动9001即可,访问: http://localhost:9001/hystrix



#### 监控步骤

1、被监控者一定要有依赖

```html
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

2、8001的主启动类添加

```java
/**
 * 此配置是为了服务监控而配置，与服务容错本身无关，springcloud升级后的坑
 * ServletRegistrationBean因为SpringBoot的默认路径不是 “/hystrix.stream"
 * 只要在自己的项目里配置上下的servlet就可以了
 */
@Bean
public ServletRegistrationBean getServlet() {
    HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet() ;
    ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
    registrationBean.setLoadOnStartup(1);
    registrationBean.addUrlMappings("/hystrix.stream");
    registrationBean.setName("HystrixMetricsStreamServlet");
    return  registrationBean;
}
```
3、启动服务，指定监9001监控8001

记录SpringCloud启用 Hystrix 仪表盘功能出现Unable to connect to Command Metric Stream.的异常解决方案：

在对应dashboard工程9001加入如下配置即可

```yml
hystrix:
  dashboard:
    proxy-stream-allow-list: "localhost"

```