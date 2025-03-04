> 在本专题系列文章中，我们将根据 EMQ 在车联网领域的实践经验，从协议选择等理论知识，到平台架构设计等实战操作，与大家分享如何搭建一个可靠、高效、符合行业场景需求的车联网平台。
>
> 在此之前，我们已经介绍了[车联网场景中的 MQTT 协议](https://www.emqx.com/zh/blog/mqtt-for-internet-of-vehicles)，以及如何根据实际业务需求进行[车联网 TSP 平台场景中的 MQTT 主题设计](https://www.emqx.com/zh/blog/mqtt-topic-design-for-internet-of-vehicles)。接下来，我们就需要考虑如何将消息数据进行高质量的安全传输。在本篇文章中，我们将借助 MQTT 协议的 QoS 特性，介绍车联网场景中的 MQTT 消息 QoS 设计，保障数据传输质量。

## 前言

车联网场景下会产生海量数据，这些数据可以作为车辆诊断的基础，保障车辆安全稳定地运行；也可以与手机等基础设施进行联动，以提供更好的行车体验。国家与行业也陆续出台了相关政策文件，如《汽车驾驶自动化分级》、《国家车联网产业标准体系建设指南》、《车联网信息服务数据安全技术要求》等，对车联网数据传输提出了更高要求。通信的安全、稳定、可靠自始至终都是车联网亘古不变的话题，因此一套完善的数据传输保障方案也是车联网业务中不可忽视的一部分。

## MQTT 协议中的 QoS 等级

作为现如今车联网行业数据通信协议的首选，[MQTT 协议](https://www.emqx.com/zh/mqtt)中规定了消息服务质量（Quality of Service，以下简称 QoS）。QoS 保证了在不同的网络环境下消息传递的可靠性，可作为车联网场景中保障消息可靠性传输的首要实现技术。

MQTT 设计了 3 个 QoS 等级：

- **QoS 0**

  消息最多传递一次，如果当时 [MQTT 客户端](https://www.emqx.io/zh/mqtt-client)不可用，则会丢失该消息。Sender (可能是 Publisher 或者 Broker) 发送一条消息之后，就不再关心它有没有发送到对方，也不设置任何重发机制。

  ![MQTT QoS 0](https://static.emqx.net/images/fb046bde08b7cd1e653d3eaacde480fc.png)

- **QoS 1**

  消息传递至少 1 次。包含了简单的重发机制，Sender 发送消息之后等待接收者的 ACK，如果没收到 ACK 则重新发送消息。这种模式能保证消息至少能到达一次，但无法保证消息重复。

  ![MQTT QoS 1](https://static.emqx.net/images/8a707edb6b019f4c62e5e25fa3345030.png)

- **QoS 2**

  消息仅传送一次。设计了重发和重复消息发现机制，保证消息到达对方并且严格只到达一次。

  ![MQTT QoS 2](https://static.emqx.net/images/752c86832c5328c428120a81596ee388.png)

## 车联网场景中的消息 QoS 设计

首先需要明确的是 QoS 级别越高，消息交互越复杂，系统资源消耗越大，所以 QoS 等级不是设置的越高越好。应用程序可以根据自己的网络场景和业务需求，选择合适的 QoS 级别。

根据车联网信息服务相关数据的属性和特征，我们可以将其分为六类：基础属性类数据、车辆工控类数据、环境感知类数据、车控类数据、应用服务类数据和用户个人信息。那么在不同的车联网场景中如何选择 MQTT QoS 等级呢？

- **以下情况下可以选择 QoS 0**

  可以接受消息偶尔丢失的场景下可以选择 QoS 0。

  车联网提供的与娱乐相关的多媒体服务，如天气预报等数据等。还有部分涉车服务类数据，如车辆历史行车数据的上报、历史行车操作数据等。

- **以下情况下可以选择 QoS 1**

  **车联网大部分场景都是选用 QoS1，它实现了系统资源性能和消息实时性、可靠性最优化。**

  QoS 1 广泛运用于控车消息、行车上报数据（含新能源国标和企标）、交通安全管控类数据，和交通安全、道路安全相关的预警数据。

- **以下情况下可以选择 QoS 2**

  对于不能忍受消息丢失，且不希望收到重复的消息，数据完整性与及时性要求较高的场景，可以选择 QoS 2。

  车联网场景中 QoS 2 的应用并不多，虽然其可以增加消息可靠性，但同时也使资源消耗和消息时延大幅增加。QoS 2 主要运用于对数据完整性与及时性要求较高的银行、消防、航空等行业，有些主机厂的行车告警和车辆充电桩计费费单消息会选择采用 QoS 2。

**特别提醒**

需要注意的是 [MQTT 发布与订阅](https://www.emqx.com/zh/blog/mqtt-5-introduction-to-publish-subscribe-model)操作中的 QoS 代表了不同的含义，发布时的 QoS 表示消息发送到 [MQTT 服务器](https://www.emqx.io/zh) 使用的 QoS 等级，订阅时的 QoS 表示 MQTT Broker 向自己转发消息时可以使用的最大  QoS 等级。需要保障发送与订阅的 QoS 一致，才能确保最终收到的消息是固定的 QoS 等级，否则会出现消费降级的情况。例如：A 发送的消息 QoS 为 2，B 订阅的消息 QoS 为1，则最终接收到消息的 QoS 为 1。 

## EMQX 基于 QoS 等级的消息传输保障

为了更好地保障车联网过程中人-车-路-网-云之间数据传递的安全可靠，同时提高消息吞吐效率，减少网络波动带来的影响，[云原生分布式物联网消息服务器 EMQX](https://www.emqx.com/zh/products/emqx) 在全面适配 QoS 信令交互的基础上，还设计了飞行窗口、消息队列、消息全链路追踪和离线消息存储等功能来提高消息可靠性。

飞行窗口的设计可允许多个未确认的 QoS 1 和 QoS 2 报文同时存在于网路链路上，消息队列则可以满足在消息链路中消息超出飞行窗口的同时对消息进行进一步存储，以满足客户端离线时未接收的消息或者未确认数据消息的存储需求。飞行窗口同时也有 upgrade_qos 参数实现根据订阅强制升级 QoS 之类的功能，可实现 QoS 等级的一致性，确保不会出现消费降级的情况。此外，EMQX 还可提供限制业务按区域接入实现不同的 QoS 等级、数据桥接 QoS 管理、MQTT-SN 协议 QoS 管理等能力，均为车联网场景下的消息可靠传输提供了有力保障。

下载体验：[https://www.emqx.com/zh/try?product=enterprise](https://www.emqx.com/zh/try?product=enterprise) 

## 结语 

通过本文我们可以看到，MQTT 协议的 QoS 特性对于车联网场景下消息数据的安全传输具有重要意义。作为完整支持 MQTT 协议标准的云原生分布式消息服务器，EMQX 在产品设计中充分利用 MQTT 协议的特性优势，为物联网平台与应用构建提供可靠的数据连接、移动、处理与集成。

## 本系列中的其它文章

- [车联网平台搭建从入门到精通 01 | 车联网场景中的 MQTT 协议](https://www.emqx.com/zh/blog/mqtt-for-internet-of-vehicles)

- [车联网平台搭建从入门到精通 02 | 千万级车联网 MQTT 消息平台架构设计](https://www.emqx.com/zh/blog/mqtt-messaging-platform-for-internet-of-vehicles)

- [车联网平台搭建从入门到精通 03 | 车联网 TSP 平台场景中的 MQTT 主题设计](https://www.emqx.com/zh/blog/mqtt-topic-design-for-internet-of-vehicles)

- [车联网平台搭建从入门到精通 05 | 车联网平台百万级消息吞吐架构设计](https://www.emqx.com/zh/blog/million-level-message-throughput-architecture-design-for-internet-of-vehicles)

- [车联网平台搭建从入门到精通 06 | 车联网通信安全之 SSL/TLS 协议](https://www.emqx.com/zh/blog/ssl-tls-for-internet-of-vehicles-communication-security)
