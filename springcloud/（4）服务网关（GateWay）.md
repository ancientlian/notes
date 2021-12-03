![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ody5tb2tlc3BhY2UuY24vaW1hZ2VzLzIwMjAvMDcvMTQvMDY5YjZlMjdkMTI0ZGIwZDcxMGQyZDE1Y2MyZmM5ZTcucG5n?x-oss-process=image/format,png)

相关概念

路由： 路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，就是根据某些规则，将请求发送到指定服务上。

断言： 断言用于匹配 HTTP 请求中的所有内容（请求头/请求参数），如果请求与断言相匹配则进行路由。

过滤： 实现的过滤器可以在请求被路由前或者之后，对请求进行修改。



# Gateway



![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ody5tb2tlc3BhY2UuY24vaW1hZ2VzLzIwMjAvMDcvMTQvOTVhYjBmZDdkOTJiYTdmNWNmNzY3NGQxNDQ0ZTJmNzQucG5n?x-oss-process=image/format,png)



## GateWay的使用

1. 新建module
2. pom修改，注意去除actuator（*注：如果启动GateWay报错可能是GateWay模块引入了web和监控的starter依赖,需要移除*）
3. 配置文件1

```yml
server:
port: 9527

spring:
application:
name: cloud-gateway

eureka:
instance:
hostname: cloud-gateway-service
client:
register-with-eureka: true
fetch-registry: true
service-url:
defaultZone: http://eureka7001.com:7001/eureka
```
4. 主启动类

5. 配置文件2，针对pay模块,设置路由

```yml
    spring:
      application:
        name: cloud-gateway
      cloud:
        gateway:
          routes:
            - id: payment_routh           # 路由的 ID，没有固定规则但要求唯一
              uri: http://localhost:8001  # 匹配后蹄冻服务的路由地址
              predicates:
                - Path=/payment/get/**     # 断言，路径相匹配的进行路由
            - id: payment_routh2           # 路由的 ID，没有固定规则但要求唯一
              uri: http://localhost:8001  # 匹配后蹄冻服务的路由地址
              predicates:
                - Path=/payment/lb/**     # 断言，路径相匹配的进行路由
```


## 硬编码配置GateWay

GateWay的网关配置，除了支持配置文件，还支持硬编码方式，使用硬编码配置GateWay：





## 配置动态路由

默认情况下Gateway会根据注册中心的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能

1、修改GateWay模块的配置文件`uri: lb://cloud-payment-service #匹配后提供服务的路由地址`

```yml
 spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service	 #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service   #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            #- After=2020-02-21T15:51:37.485+08:00[Asia/Shanghai]
            #- Cookie=username,zzyy
            #- Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
```
## Pridicate断言

**我们之前在配置文件中配置了断言:**

```yml
predicates:
	- Path=/payment/get/**     # 断言，路径相匹配的进行路由
```

Gateway 包括许多内置的 Route Predicate 工厂，所有这些 Predicate 都与 HTTP 请求的不同属性匹配，且多个 Route Oredicate 工厂可以进行组合。

**After：**可以指定，只有在指定时间后，才可以路由到指定微服务，在指定的时间之前访问，都会报404。

**before：**与after类似,他说在指定时间之前的才可以访问

**between：**需要指定两个时间,在他们之间的时间才可以访问

**cookie：**只有包含某些指定cookie(key,value),的请求才可以路由

**Header：**只有包含指定请求头的请求,才可以路由

**Host：**只有指定主机的才可以访问

**Method：**只有指定请求才可以路由，比如get请求…

**Path：**只有访问指定路径，才进行路由，我们已经用过了

**Query：**必须带有请求参数才可以访问





## Filter

Gateway 内置了多种路由过滤器，由 GatewayFilter 的工厂类生成，过滤器的类型可以在[官网](https://cloud.spring.io/spring-cloud-gateway/reference/html/)查看。

**生命周期**：在请求进入路由之前，和处理请求完成，再次到达路由之前

**种类：**

- GatewayFilter：单一的过滤器
- GlobalFilter：全局过滤器



**自定义过滤器**

需要实现两个接口`GlobalFilter, Ordered`

```java
package com.example.springcloud.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Date;

@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange serverWebExchange, GatewayFilterChain gatewayFilterChain) {
        log.info("****come in MyLogGateWayFilter：" + new Date());
        String uname = serverWebExchange.getRequest().getQueryParams().getFirst("uname");
        if (uname == null) {
            log.info("****用户名为空，非法用户");
            serverWebExchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return serverWebExchange.getResponse().setComplete();
        }
        return gatewayFilterChain.filter(serverWebExchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```