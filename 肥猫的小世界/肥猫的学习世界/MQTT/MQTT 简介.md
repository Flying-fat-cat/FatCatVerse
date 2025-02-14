## 1、概述
MQTT(消息队列遥测传输)是 ISO 标准(ISO/IEC PRF 20922)下基于发布/订阅范式的消息协议 
本文主要介绍 MQTT 协议 3.1.1 的内容。

### 1.1、背景

随着 5G 时代的来临，万物互联的伟大构想正在成为现实。联网的 物联网设备 在 2018 年已经达到了 70 亿，在未来两年，仅智能水电气表就将超过 10 亿。

![](肥猫的小世界/肥猫的学习世界/MQTT/图片/IOT%20设备数量2015-2025.png)


海量的设备接入和设备管理对网络带宽、通信协议以及平台服务架构都带来了很大挑战。对于 物联网协议 来说，必须针对性地解决物联网设备通信的几个关键问题：其网络环境复杂而不可靠、其内存和闪存容量小、其处理器能力有限。

MQTT 协议 是基于发布/订阅模式的物联网通信协议，凭借简单易实现、支持 QoS、报文小等特点，占据了物联网协议的半壁江山。

### 1.2、MQTT 协议的诞生

MQTT was created by [Andy Stanford-Clark](https://en.wikipedia.org/wiki/Andy_Stanford-Clark) of IBM, and Arlen Nipper (then of Arcom Systems, later CTO of Eurotech).[  
](https://www.emqx.com/zh/blog/what-is-the-mqtt-protocol#fn-3)

据 Arlen Nipper 在一 [IBM Podcast 上的自述](https://www.ibm.com/podcasts/software/websphere/connectivity/piper_diaz_nipper_mq_tt_11182011.pdf)，MQTT 原名是 `MQ TT`， 注意 `MQ` 与 `TT`之间的空格，其全称为: MQ Telemetry Transport，是九十年代早期，他在参与 Conoco Phillips 公司的一个原油管道数据采集监控系统(pipeline SCADA system)时，开发的一个实时数据传输协议。它的目的在于让传感器通过带宽有限的 [VSAT](https://en.wikipedia.org/wiki/Very-small-aperture_terminal) ，与 IBM 的 MQ Integrator 通信。由于 Nipper 是遥感和数据采集监控专业出身，所以按业内惯例给了个 `MQ TT` 的名字。

## 2、MQTT 协议核心特点

按照 Nipper 的介绍，MQTT 必须简单容易实现，必须支持 QoS(设备网络环境复杂)，必须轻量且省带宽(因为那时候带宽很贵)，必须数据无关(不关心 Payload 数据格式)，必须有持续地会话感知能力(时刻知道设备是否在线)。

### 2.1、发布订阅模式

发布订阅模式是传统 Client/Server 模式的一种解耦方案。发布者通过 Broker 与消费者之间通信，Broker 的作用是将收到的消息通过某种`过滤规则`，正确地发送给消费者。发布/订阅模式 相对于 客户端/服务器模式 的好处在于：

- 发布者和消费者之间不必预先知道对方的存在，比如不需要预先沟通对方的 IP Address 和 Port
- 发布者和消费者之间不必同时运行。因为 Broker 是一直运行的。

在 MQTT 协议里，上面提到的 `过滤规则` 是 `Topic`。比如：所有发布到 `news` 这个 Topic 的消息，都会被 Broker 转发给已经订阅了 `news` 的订阅者:

![](https://img2022.cnblogs.com/blog/1846282/202208/1846282-20220805184022500-1343389516.png)

上图中订阅者预先订阅了 `news`，然后发布者向 Broker 发布了一条消息 "some msg" 并指定发布到 `news` 主题，Broker 通过 Topic 匹配，决定将这条消息转发给订阅者。

MQTT 的 Topic 有层级结构，并且支持通配符 `+` 和 `#`:

- `+` 是匹配单层的通配符。比如 `news/+` 可以匹配 `news/sports`，`news/+/basketball` 可匹配到 `news/sports/basketball`。
- `#` 是一到多层的通配符。比如 `news/#` 可以匹配 `news`、 `news/sports`、`news/sports/basketball` 以及 `news/sports/basketball/x` 等等。

MQTT 的主题是不要预先创建的，发布者发送消息到某个主题、或者订阅者订阅某个主题的时候，Broker 就会自动创建这个主题。

### 2.2、带宽消耗最小原则

MQTT 协议将协议本身占用的额外消耗最小化，消息头部最小只需要占用 2 个字节。MQTT 的消息格式分三部分：

|固定长度头部，2 个字节，所有消息类型里都有|
|---|
|可变长度头部，只有某些消息类型里有|
|Payload，只有某些消息类型里有|

MQTT 的主要消息类型有：

- CONNECT / CONNACK
- PUBLISH / PUBACK
- SUBSCRIBE / SUBACK
- UNSUBSCRIBE / UNSUBACK
- PINGREQ / PINGRESP
- DISCONNECT

其中 PINGREQ / PINGRESP 和 DISCONNECT 报文是不需要可变头部的，也没有 Payload，也就是说它们的报文大小仅仅消耗 2 个字节。

在 CONNECT 报文的可变长度头部里，有个 Protocol Version 的字段。为了节省空间，只有一个字节。所以版本号不是按照字符串 "3.1.1" 存放的，而是使用数字 4 来表示 3.1.1 版本。

### 2.3、可选的 QoS 等级

为适应设备不同的网络环境，MQTT 设计了 3 个 QoS 等级，0, 1, 2:

- _At most once_ (0)
- _At least once_ (1)
- _Exactly once_ (2)

QoS 0 是一种 "fire and forget" 的消息发送模式：Sender (可能是 Publisher 或者 Broker) 发送一条消息之后，就不再关心它有没有发送到对方，也不设置任何重发机制。

QoS 1 包含了简单的重发机制，Sender 发送消息之后等待接收者的 ACK，如果没收到 ACK 则重新发送消息。这种模式能保证消息至少能到达一次，但无法保证消息重复。

QoS 2 设计了略微复杂的重发和重复消息发现机制，保证消息到达对方并且严格只到达一次。

### 2.4、会话保持

MQTT 没有假设设备或 Broker 使用了 TCP 的保活机制，而是设计了协议层的保活机制：在 CONNECT 报文里可设置 Keepalive 字段，来设置保活心跳包 PINGREQ/PINGRESP 的发送时间间隔。当长时间无法收到设备的 PINGREQ 的时候，Broker 就会认为设备已经下线。

总的来说，Keepalive 有两个作用：

- 发现对端死亡或者网络中断
- 在长时间无消息交互的情况下，保持连接不被网络设备断开

对于那些想要在重新上线后，重新收到离线期间错过的消息的设备，MQTT 设计了持久化连接：在 CONNECT 报文里可设置 CleanSession 字段为 False，则 Broker 会为终端存储：

- 设备所有的订阅
- 还未被设备确认的 QoS1 和 QoS 消息
- 设备离线时错过的消息

### 2.5、在线状态感知

MQTT 设计了遗愿(Last Will) 消息，让 Broker 在发现设备异常下线的情况下，帮助设备发布一条遗愿消息到指定的主题。

## 3、MQTT 5.0

MQTT 5.0 增加了许多新特性，详细信息可参考：https://www.emqx.com/zh/mqtt/mqtt5

参考资料链接：
- https://www.emqx.com/zh/blog/what-is-the-mqtt-protocol
- https://www.cnblogs.com/wuyongyin/p/16171272.html