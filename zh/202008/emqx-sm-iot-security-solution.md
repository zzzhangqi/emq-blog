## 方案背景  

在 5G 万物互联时代，将物理世界的数据进行数字化采集、传输和分析，最终通过丰富的物联网应用实现智慧化，是未来发展的大势所趋 。随着物联网在各行各业快速和深入的发展应用，各种终端设备联网的需求强烈。在物联网快速发展的同时，考虑到使用场景的开放性、不可监督性、无线传输安全的脆弱性、网络环境的复杂性，物联网系统面临敏感数据泄露、系统攻击面增大的风险。加上近年来物联网网络安全事件逐渐增多，安全保障越来越受到重视，各种安全技术进一步发展。 

近年来国家推出了多个物联网安全规范与要求，规范在多个物联网行业内使用国密加密算法保障数据安全传输。

2017 年 12 月 29 日正式发布 GB/T 35592-2017 《公安物联网感知终端接入安全技术要求》 提出：数据传输应采用国家规定的加密算法，以端到端信道加密方式保障数据传输安全。

2018 年 2 月 1 日正式实施 T/CGAS 003-2017 《民用智能燃气表通用技术要求》中提出 ：应采用符合国家密码管理政策的加解密算法，对称密码算法宜使用国密 SM1算法、国密 SM4 算法，非对称密码算法宜使用国密 SM2 算法。

2019 年 7 月 26 日 工信部等十部委联合发文《加强工业互联网安全工作的指导意见》提出：指导企业完善研发设计、工业生产、运维管理、平台知识机理和数字化模型等数据的防窃密、防篡改和数据备份等安全防护措施，鼓励商用密码在工业互联网数据保护工作中的应用。

对于政府与企业的物联网平台建设过程中，需要重点考虑物联网平台接入与数据加密模块是否具备综合使用国密 SM2/SM3/SM4/SM9 系列算法，解决物联网系统中身份认证、数据安全、传输安全、访问控制等多种安全问题的能力。

## EMQX 物联网安全方案

EMQ 作为国际领先的物联网设备接入与数据处理解决方案提供商，与安全领域合作伙伴郑州信大捷安信息技术股份有限公司联合推出了物联网综合安全接入方案。本方案涵盖了从设备到边缘端到云端，从链路层到数据报文层的多级安全接入能力，为政企用户在物联网接入领域提供了全方位的安全保障。

#### EMQX 物联网数据接入链路层安全方案

![EMQX链路层国密支持方案架构图 .png](https://static.emqx.net/images/8243e84d26415072862710a3b0579c29.png)

EMQ 作为物联网从边缘到云端的设备接入解决方案提供商，在设备接入安全方面提供了物联网数据传输链路层安全接入方案。

EMQX  安全接入方案的能力包括 ：

    1) 根据网络特点，边缘端服务器与云端数据中心与 IoT 终端协同工作，实现国密TLS通道加密服务，全面支持IoT终端安全接入边缘端，边缘端安全接入云端；
    
    2）云端 EMQX 集群和边缘端 EMQ 节点通过内网LVS对接安全接入网关，实现国密安全服务能力调用；安全接入网关对接后端安全认证区，与证书/密钥管理系统、服务器密码机等安全设备连接，对设备接入安全提供保障
    
    3）安全接入网关也可集群部署、弹性扩容，根据部署环境，支持硬件和软件的部署方式，不同部署方式适用于不同场景。硬件部署方式安全性好、性能高、成本高，可省略密码机；软件部署方式安全性次之、性能低、成本低，建议配合密码机一起使用。


#### EMQX 数据报文加解密国密算法支持

除了链路层加密协议接入能力外，EMQX 还提供对协议报文加解密的国密算法支持。EMQX 消息中间件在消息接入或发出过程中对消息报文提供国密加解密能力。EMQX 通过在接入层调用加密机接口，实现对物联网数据报文的实时加解密，使数据传输更加安全。

![EMQX数据报文层国密支持方案架构图 .png](https://static.emqx.net/images/9edbd22043cba7b496248416b423fc84.png)



#### 终端安全

在终端安全方面，EMQ 的物联网安全合作伙伴提供多种终端加密安全方案，提供包括：安全芯片、虚拟硬件密码模块、安全通信模组等多种设备端安全方案，其特点和应用方式如下：

| **密码模块**     | **产品名称**                           | **应用特点**                                       | **应用方式**                                       |
| ---------------- | -------------------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| 安全芯片         | 安全芯片                               | 安全等级高、贴片集成，需要制版                     | 内嵌终端主板，支持 USB/SD/SPI/UART/I2C 通信        |
| 安全密码模块     | 安全 TF 卡/安全 USBKey /安全智能薄膜卡 | 安全等级高、适用于不同终端类型，终端改造量小       | 外置在终端接口，包括 SD 卡槽/ USB 接口/ SIM 卡接口 |
| 安全通信模组     | LTE 安全模组/ NB-IoT 安全模组          | 安全等级高、具备通信功能和安全功能，集成调试工作少 | 外置 mini-PCIE 卡槽/  内嵌终端主板                 |
| 虚拟硬件密码模块 | VHSM                                   | 安全等级次之，软件形式，易集成，终端改造量小       | 软件集成                                           |



综上所述， EMQ 在安全方面从设备、边缘端到云端，从连接层到数据层提供了完整的安全方案，支持基于国密算法的完整解决方案，帮助政府、企业更加安全高效的应用物联网技术实现新时代数字化转型。
