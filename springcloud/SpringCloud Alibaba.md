# Nacos

关于 Nacos Spring Cloud 的详细文档请参看：[Nacos Config](https://github.com/spring-cloud-incubator/spring-cloud-alibaba/wiki/Nacos-config) 和 [Nacos Discovery](https://github.com/spring-cloud-incubator/spring-cloud-alibaba/wiki/Nacos-discovery)。

- 通过 Nacos Server 和 spring-cloud-starter-alibaba-nacos-config 实现**配置的动态变更。**
- 通过 Nacos Server 和 spring-cloud-starter-alibaba-nacos-discovery 实现服务的注册与发现。

## 服务注册中心

依赖

```html
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

### 基于Nacos的服务提供者



### 基于Nacos的服务消费者



### 服务注册中心对比

a：可用性 c：强一致性 p：多备份性

Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性）

==Nacos支持AP和CP模式的切换==

C是所有节点在同一时间看到的数据是一致的;
而A的定义是所有的请求都会收到响应。

何时选择使用何种模式?

一般来说，如果不需要存储服务级别的信息且服务实例是通过nacos client注册， 并能够保持心跳上报,那么就可以选择AP模式。

当前主流的服务如Spring cloud和Dubbo服务,都适用于AP模式，AP模式为了服务的可能性而减弱了一致性, 因此AP模式下只支持注册临时实例。

如果需要在服务级别编辑或者存储配置信息，那么CP是必须, K8S服务和DNS服务则适用于CP模式。

CP模式下则支持注册持久化实例，此时则是以Raft协议为集群运行模式，该模式下注册实例之前必须先注册服务,如果服务不存在,则会返回错误。

| 组件名    | 语言 | CAP  | 服务健康检查 | 对外暴露接口 | Spring Cloud集成 |
| --------- | ---- | ---- | ------------ | ------------ | ---------------- |
| Eureka    | Java | AP   | 可配支持     | HTTP         | 已集成           |
| Zookeeper | Java | CP   | 支持         | 客户端       | 已集成           |
| Consul    | Go   | CP   | 支持         | HTTP/DNS     | 已集成           |



## 服务配置中心

### 基础配置

cloudalibaba-config-nacos-client3377

1、pom文件

2、yml文件：bootstrap、application

Nacos同springcloud-config- 样,在项目初始化时, 要保证先从配置中心进行配置拉取,
拉取配置之后，才能保证项目的正常启动。

springboot中配置文件的加载是存在优先级顺序的，bootstrap优先级高于application



3、主启动类

4、controller

重点是`@RefreshScope`标签

```java
@RestController
@RefreshScope   //支持nacos的动态刷新功能
public class ConfigClientController
{
    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/config/info")
    public String getConfigInfo() {
        return configInfo;
    }
}
```

5、在nacos中添加配置信息

https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html

在 Nacos Spring Cloud 中，`dataId` 的完整格式如下：

```plain
${prefix}-${spring.profiles.active}.${file-extension}
```

- `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix`来配置。
- `spring.profiles.active` 即为当前环境对应的 profile，详情可以参考 [Spring Boot文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html#boot-features-profiles)。 **注意：当 `spring.profiles.active` 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}.${file-extension}`**
- `file-exetension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension` 来配置。目前只支持 `properties` 和 `yaml` 类型。



==注意，yaml不能缩写成yml==

自带动态刷新：修改下Nacos中的yaml配置文件，再次调用查看配置的接口，就会发现配置已经刷新



### 分类配置

#### Namespace+Group+Data ID三者关系？为什么这么设计？

Nacos有分类管理的操作。抛出三个概念，**namespace(命令空间)、group(分组)、dataID**。

dataID：Nacos 中的某个配置集的 ID。配置集 ID 是组织划分配置的维度之一。Data ID 通常用于组织划分系统的配置集。一个系统或者应用可以包含多个配置集，每个配置集都可以被一个有意义的名称标识。Data ID 通常采用类 Java 包（如 com.taobao.tc.refund.log.level）的命名规则保证全局唯一性。此命名规则非强制。**这个概念来自于官方文档，说人话就是配置文件的名字，相当于主键的作用**

group(分组): Nacos 中的一组配置集，是组织配置的维度之一。通过一个有意义的字符串（如 Buy 或 Trade ）对配置集进行分组，从而区分 Data ID 相同的配置集。当您在 Nacos 上创建一个配置时，如果未填写配置分组的名称，则配置分组的名称默认采用 DEFAULT_GROUP 。配置分组的常见场景：不同的应用或组件使用了相同的配置类型，如 database_url 配置和 MQ_topic 配置。**说人话，就是可以分组，不同的系统或微服务的配置文件可以放在一个组里。比如用户系统和订单系统的配置文件都可以放在同个组中。**

命名空间(namespace): 用于进行租户粒度的配置隔离。不同的命名空间下，可以存在相同的 Group 或 Data ID 的配置。**Namespace 的常用场景之一是不同环境的配置的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200826202802200.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0pva2Vyb25lZQ==,size_16,color_FFFFFF,t_70#pic_center)

**默认情况:**
**Namespace=public, Group= DEFAULT_ GROUP,默认Cluster是DEFAULT**

Nacos默认的命名空间是public, Namespace主要用来实现隔离。
比方说我们现在有三个环境:开发测试、铲环境，我们就可以创建= E个Namespace,不同的Namespace之间是隔离的。

Group默认是DEFAULT_ GROUP, Group可以把不同的微服务划分到同一个分组里面去

Service就是微服务;一个Service可以包含多 个Cluster (集群)，Nacos默认Cluster是DEFAULT, Cluster是对指定微服务的一个虚拟划分。
比方说为了容灾，将Service微服务分别部署在了杭州机房和广州机房,
这时就可以给杭州机房的Service微服务起一个集群名称 (HZ) ,
给广州机房的Service微服务起一个集群名称(GZ)， 还可以尽量让同一个机房的微服务互相调用,以提升性能。

后是Instance,就是微服务的实例。

## Nacos集群和持久化配置



如何找到问题：查看日志start.out

修改nacos内存大小，三个注册中心内存太大了



```
JAVA_OPT="${JAVA_OPT} -server -Xms500m -Xmx500m -Xmn200m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```

一开始`server-addr: 192.168.153.128:1111 `不行，改为`server-addr: http://192.168.153.128:1111 `

可以注册进去

# Sentinel



Sentinel采用的懒加载，需要先执行一次访问才能够有显示效果



## 流控规则

资源名:唯一名称,默认请求路径

针对来源: Sentine可以针对调用者进行限流，填写微服务名，**默认default (不区分来源)**

阈值类型/单机阈值：

- **QPS** (每秒钟的请求数量) :当调用该api的QPS达到阈值的时候，进行限流

- **线程数**:当调用该api的线程数达到阈值的时候,进行限流

是否集群:不需要集群

流控模式：

- **直接：**api达到限流条件时，直接限流

- **关联：**当关联的资源达到阈值时，就限流自己

- **链路：**只记录指定链路上的流量(指定资源从入口资源进来的流量，如果达到阈值，就进行限流) [api級
  别的针对来源]

流控效果：

- **快速失败：**直接失败,抛异常
- **Warm Up：**根据codeFactor (冷加载因子，默认3)的值，从阈值/codeFactor，经过预热时长，才达到设
  置的QPS阈值
  - 官网解释https://github.com/alibaba/Sentinel/wiki/%E9%99%90%E6%B5%81---%E5%86%B7%E5%90%AF%E5%8A%A8
  - `com.alibaba.csp.sentinel.slots.block.flow.controller.WarmUpController`
  - 应用场景如：秒杀系统在开启的瞬间，会有很多流量上来,很有可能把系统打死，预热方式就是把为了保护系统，可慢慢的把流量放进来，慢慢的把阀值增长到设置的阀值。
- **排队等待：**匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效
  - 对应漏桶算法
  - `com.alibaba.csp.sentinel.slots.block.flow.controller.RateLimiterController`



## 降级规则

https://github.com/alibaba/Sentinel/wiki/%E7%86%94%E6%96%AD%E9%99%8D%E7%BA%A7

**RT (平均响应时间，秒级)**
平均响应时间  **超出阈值 **  且   **在时间窗口内通过的请求>=5**  ，两个条件同时满足后触发降级
窗口期过后关闭断路器
RT最大4900 (更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效)

**异常比列(秒级)**
**QPS>= 5**  且**异常比例(秒级统计)超过阈值**时，触发降级；时间窗口结束后，关闭降级

**异常数(分钟级)**
**异常数(分钟统计)超过阈值**时，触发降级；时间窗口结束后，关闭降级



> - 慢调用比例 (`SLOW_REQUEST_RATIO`)：选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。
> - 异常比例 (`ERROR_RATIO`)：当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。
> - 异常数 (`ERROR_COUNT`)：当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。



==Sentinel的断路器是没有半开状态的==，而Hystrix是特点（半开的状态系统自动去检测是否请求有异常，没有异常就关闭断路器恢复使用，有异常则继续打开断路器不可用。具体可以参考Hystrix）





## 热点参数限流

### Overview

何为热点？热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的 Top K 数据，并对其访问进行限制。比如：

- 商品 ID 为参数，统计一段时间内最常购买的商品 ID 并进行限制
- 用户 ID 为参数，针对一段时间内频繁访问的用户 ID 进行限制

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效。

![Sentinel Parameter Flow Control](https://github.com/alibaba/Sentinel/wiki/image/sentinel-hot-param-overview-1.png)

Sentinel 利用 LRU 策略统计最近最常访问的热点参数，结合令牌桶算法来进行参数级别的流控。热点参数限流支持集群模式。