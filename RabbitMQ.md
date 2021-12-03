# RabbitMQ

## 消息队列持久化



## 消息分发策略的机制和对比


|          | ActiveMQ | RabbitMQ | Kafka | RocketMQ |
| -------- | -------- | -------- | ----- | -------- |
| 发布订阅 | 支持     | 支持     | 支持  | 支持     |
| 轮询分发 | 支持     | 支持     | 支持  | /        |
| 公平分发 | /        | 支持     | 支持  | /        |
| 重发     | 支持     | 支持     | /     | 支持     |
| 消息拉取 | /        | 支持     | 支持  | 支持     |

### 公平分发

公平分发的本质就是能者多劳
1，使用公平分发，必须关闭自动应答ack，然后改成手动应答方式。

2，每个消费者发送确认消息之前，消息队列不发送下一个消息到消费者，一次只处理一个消息。
限制发送给同一个消费者不得超过1条消息。

3，消费者增加了channel.basicQos(1)控制同一消息消费次数

## 高可用和高可靠

集群模式1 - Master-slave主从共享数据的部署方式

集群模式2 - Master- slave主从同步部署方式

集群模式3 -多主集群同步部署模式

集群模式4 - 多主集群转发部署模式

集群模式5 - Master-slave与Breoker-cluster组合的方案

反正终归三句话:
1:要么消息共享，
2:要么消息同步
3:要么元数据共享

### 高可靠

所谓高可用是指:是指系统可以无故障低持续运行，比如一一个系统突然崩溃,报错,异常等等并不影响线上业务的正常运行，出错的几率极低，就称之为:高可靠。
在高并发的业务场景中,如果不能保证系统的高可靠，那造成的隐患和损失是非常严重的。
如何保证中间件消息的可靠性呢?可以从两个方面考虑:
1:消息的传输:通过协议来保证系统间数据解析的正确性。
2:消息的存储可靠:通过持久化来保证消息的可靠性。





## 角色分类

### none

不能访问 management plugin

*management*
用户可以通过AMQP做的任何事外加：
列出自己可以通过AMQP登入的virtual hosts  
查看自己的virtual hosts中的queues, exchanges 和 bindings
查看和关闭自己的channels 和 connections
查看有关自己的virtual hosts的“全局”的统计信息，包含其他用户在这些virtual hosts中的活动。

### policymaker

management可以做的任何事外加：
查看、创建和删除自己的virtual hosts所属的policies和parameters

### monitoring

management可以做的任何事外加：
列出所有virtual hosts，包括他们不能登录的virtual hosts
查看其他用户的connections和channels
查看节点级别的数据如clustering和memory使用情况
查看真正的关于所有virtual hosts的全局的统计信息

### administrator

policymaker和monitoring可以做的任何事外加:
创建和删除virtual hosts
查看、创建和删除users
查看创建和删除permissions
关闭其他用户的connections



## AMQP



## 核心组成部分

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210317090601892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwMDIyMDU1,size_16,color_FFFFFF,t_70)

### 核心概念

**Server:** 又被称为Broker,接受客户端的连接，实现AMQP服务。就是我们自己安装的rabbitmq-server
**Connection:** 连接，应用程序与Broker的网络连接（使用的是TCP/IP连接）
**Channel:**网络信道，几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道，每个通道Channel代表一个会话任务。
**Message：**消息，服务于应用程序之间传递的数据，由Properties和body组成，Properties可以对消息进行修饰，比如消息的优先级，延迟等高级特征，Body则是消息体的内容。
**Virtual Host**：虚拟地址，用于逻辑层隔离，最上层的消息路由，一个虚拟机理由可以有若干的Exchange和Queue，同一个虚拟机里面不能有相同名字的Exchange。
**Exchange**: 交换机，接收消息，根据路由键发送消息到绑定的队列（不具备储存消息的能力），一定会有
**Bindings：** Exchange和Queue之间的虚拟连接，Binding中可以保护多个routing key.
**Routing key:** 是一个路由规则，虚拟机可以用它来确定如何路由一个特定消息。
**Queue**:队列，也是MessageQueue队列，保存消息转发给消费者

## 整体架构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210317095245521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwMDIyMDU1,size_16,color_FFFFFF,t_70)

## 操作流程

第一：获取Conection
第二：获取Channel
第三：定义Exchange，Queue
第四：使用一个RoutingKey将Queue Binding到一个Exchange上
第五：通过指定一个Exchange和一个RoutingKey来将消息发送到对应的Queue上，
第六：Consumer在接收时也是获取connection，接着获取channel，然后指定一个Queue，到Queue上取消息，它对Exchange，RoutingKey及如何Binding都不关心，到对应的Queue上去取消息就行了。

注意：一个PublisherClient发送消息，哪些ConsumerClient可以收到消息，在于Exchange，RoutingKey，Queue的关系上

## 运行流程

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021031709590445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwMDIyMDU1,size_16,color_FFFFFF,t_70)

## 支持消息的模式

### 简单模式 Simple

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9zZWdtZW50ZmF1bHQuY29tL2ltZy9yZW1vdGUvMTQ2MDAwMDAyMzEwNzgzNA?x-oss-process=image/format,png)

### Work queues 工作队列

![img](https://www.rabbitmq.com/img/tutorials/python-two.png)

#### 轮询模式

一个消费者一条，按均分配



#### 公平分发

根据消费者能力进行公平分发，处理快的处理的多，处理慢的少；按劳分配

公平分发一定需要手动应答





### Publish/Subscribe 发布与订阅 fanout

![img](https://www.rabbitmq.com/img/tutorials/python-three.png)

案例













### Routing 路由模式

![img](https://www.rabbitmq.com/img/tutorials/python-four.png)

###  [Topics](https://www.rabbitmq.com/tutorials/tutorial-five-python.html)

![img](https://www.rabbitmq.com/img/tutorials/python-five.png)

`*`：代表一定是1级以上，一定要有一个

`#`：代表0或者多级，可有可无



### 参数模式 headers





## 完整的创建流程Demo

```java
/**
 * 开始声明交换机
 * @params3 是否持久化，持久化的交换机不会随着服务器重启造成丢失
 * */
channel.exchangeDeclare(exchangeName, type, true);

/**
 * 声明队列,这个队列并不存在
 * @params3
 * */
channel.queueDeclare("queue5", true, false, false, null);
channel.queueDeclare("queue6", true, false, false, null);
channel.queueDeclare("queue7", true, false, false, null);

/**
 * 绑定队列和交换机的关系
 * */
channel.queueBind("queue5", exchangeName, "email");
channel.queueBind("queue6", exchangeName, "email");
channel.queueBind("queue7", exchangeName, "order");
```



## 使用场景

1. 同步异步的问题（串行）
2. 高内聚，低耦合
3. 流量削峰
4. 分布式事务的可靠消费和可靠生产
5. 索引、缓存、静态化处理的数据同步
6. 流量监控
7. 日志监控（ELK）
8. 下单、订单分发、抢票



## 整合springboot