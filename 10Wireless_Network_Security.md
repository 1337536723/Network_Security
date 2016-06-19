# 无线网络安全(Wireless Network Security)

## 无线网络的组成要素
- 无线网络主要由以下部分组成
    - 端设备
        - 手机，笔记本电脑…
        - 这些设备可以是移动的也可以是静止的

    - 基站
        - 一般与有线网络相连
        - 上面有接入点(AP, access point)负责无线接入

    - 无线链路
        - 在端设备和基站间通过无线链路传输信号
        - 不同的无线链路技术有不同的传输速率和距离

## 无线网络的组网方式
- 组网方式
    - 基站模式(infrastructure mode)
        - 基站连接有线网
        - 终端设备以无线的方式连接基站
        - 典型协议：WiFi

    - ad hoc自组织模式
        - 不需要基站，终端设备自组织网络
        - 典型协议：蓝牙

## Wi-Fi的安全

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WiFi_Security1.png)

802.11i协议规定WiFi的数据传输分为如下几个过程:

- **发现**
    - 移动终端与接入点(AP)

- **认证**
    - 移动终端设备和认证服务器(AS)进行认证

- **密钥管理**
    - 移动终端设备和认证服务器进行秘钥交换

- **数据传输**
    - 移动终端设备先将数据进行加密后送给AP，AP再进行转发连接结束

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WiFi_Security2.png)

### 发现过程

STA（station，站，一般指带无线网卡的电脑或手机等）和AP相互确认身份，协商一系列安全策略，并建立连接以便将来进行通信

第一步中AP可以每隔一段时间发一个beacon信号，从而其他终端设备可以知道附近存在哪些网络，也可以不发；第三步相当于握手，会进行后面用到的安全算法的协商

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WiFi_Security3.png)

### 认证过程

- 认证者(AP)负责与申请者(终端设备)通信并将认证信息传递给认证服务器(AS)

- 认证者根据认证结果，决定控制端口处于认证或非认证状态

- 与此同时在这一步中还实现了传输控制的功能，认证服务器可以控制认证者有多少端口处于认证状态

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WiFi_Security4.png)

### 密钥管理
- 秘钥分为两种
    - 终端和AP进行单播时使用成对秘钥
        - 成对秘钥的生成非常复杂，需要从PMK(Pair-wise Master Key成对主秘钥)导出，而这个是在认证过程中产生的，因此在认证过程中需要认证服务器将PMK的信息交给AP

    - 终端和AP进行多播时使用组秘钥

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WiFi_Security5.png)

### 加密的数据传输阶段
- 定义了两种加密和完整性协议
    - TKIP（ Temporal Key Integrity Protocol，临时秘钥完整性协议）
        - 可靠性不如CCMP，因为用的是RC4的加密算法（WEP被攻破的时候很多应用WEP的硬件并不能支持CCMP协议，TKIP就是在这种情况下针对应用RC4加密算法的硬件提供的一种软件上的解决方案）
        - WPA加密用的就是TKIP的方法
    - CCMP（ Counter Mode-CBC MAC Protocol）
        - 结合AES算法和counter模式、CBC模式的加密算法
        - WPA2加密用的就是CCMP方法

## 无线应用协议(WAP)的安全

- 老一代的智能手机(比如Nokia的塞班系统)在上网时与计算机上网有很大不同
    - 手机网络传输功能有限(原来多用2G网络)
    - 传统的网页在手机上展示受到限制
    - 同时也有特殊的交互需求(比如需要单击响应)

- 由此催生了无线应用协议(WAP)专门用于处理此类需求

WAP是一套适合不同网络类型的全球无线网络协议规范，已成为通过移动电话或其他无线终端访问无线信息服务的全球实施标准

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WAP1.png)

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WAP2.png)

- 无线会话协议(Wireless Session Protocol (WSP))
    - 在无线应用协议（WAP）组中的协议，为服务的双方提供无线应用环境一个稳定的接口

- 无线传输协议(Wireless Transaction Protocol (WTP))
    - WAP传输协议层，提供传输支持，增加由WDP提供的数据报服务的可靠性

- 无线数据报协议(Wireless Datagram Protocol (WDP))

### WTLS

- WTLS是设计使用在传输层（Transport Layer）之上的安全层（Security Layer）。WTLS的功能类似全球信息网站所使用的SSL加密传输技术，可以确保资料在传输的过程中经过编码、加密处理

- WTLS的原理和SSL近似，只是有些细节针对较小频宽的通讯环境作了修正
    - 比如减少了传输的数据

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WTLS1.png)

- WTLS的工作与SSL有很大的相似性，其主要功能也是提供加密和完整性验证
    - 一个主要的不同在于由于带宽所限，不采用直接秘钥管理的办法，而是交换一个秘钥生成函数和一些相关的参数

![8](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WTLS2.png)

### WAP2
- 为加强WAP的实用性而设计的，提供了更高带宽、更快的数据传输速度、更强大的接入能力
    - 采用最新的Internet标准和协议，取消了WSP、WDP，代之以HTTP和TCP/IP

![9](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/WAP3.png)
