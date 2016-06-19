# 防火墙(Firewalls)

## 什么是防火墙
- 防火墙就好比门卫，会根据特定的规则识别并抵御非法的攻击

- 防火墙的功能
    - 实现安全机制，监控安全相关的行为并做好记录管理
    - 提供验证
    - VPN（虚拟专用网络）的主要功能相当于防火墙

- 防火墙并不能保护
    - 绕开防火墙的攻击
    - 防火墙内部留有的后门
    - 传输内容的病毒查杀


## 防火墙的四大种类

- 基于包过滤的防火墙
- 电路层的防火墙
- 应用层的防火墙
- 基于状态监测的防火墙

**注意：这四者并不是相互独立的，一个防火墙可能会实现上述的多个功能**

### 基于包过滤的防火墙
- 在网络层中进行

- 白名单里的过，黑名单里的直接在路由器上就丢掉

- 在路由器上就可以实现

- 优点：简单，代价低，速度快，对应用层透明

- 缺点：功能有限（不能针对特定应用进行过滤，因为看不到上层）；规则粗放，很难做到准确过滤

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Firewall1.png)

#### 典型的问题
1、允许了ip为1.1.1.1的数据包通过防火墙，那么黑客知道这个以后可以改自己的ip源地址

2、过滤规则都是单向的难免会有漏洞（比如只设置了单向的规则）

3、没办法做到个别过滤

### 电路层的防火墙
- 在这里电路的意思指的是TCP连接
- 在传输层拒绝某些握手请求

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Firewall2.png)

### 应用层的防火墙
- 通过成为应用程序代理（比如http代理）的做法，帮助特定应用阻挡攻击
    - 代理服务器是设置在Internet防火墙网关的专用应用级代码。这种代理服务准许网管员允许或拒绝特定的应用程序或一个应用的特定功能
    - 缺点：逻辑比较复杂，效率比较低（因此要求代理服务器处理效率要很高），针对不同的应用都要开一个这样的代理
    - 优点：能识别是什么类型的应用，并可以针对特定应用进行过滤，过滤更加精细（比如说可以提供数据源验证的功能）；同时易于管理和记录


![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Firewall3.png)

### 基于状态监测的防火墙
- 相当于在传统的包过滤防火墙的技术加上状态，并在原来只根据五元组进行过滤的基础上再根据实时的状态决定是否进行过滤

- 通过状态进行实时决定使得过滤更加准确，更加贴近应用的实际，同时也保证了一定的效率，保持了对应用层透明的特点

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Firewall4.png)

### 性能比较

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Firewall5.png)

## 非军事区(DMZ)

DMZ(demilitarized zone)，中文名称为“隔离区”，也称“非军事化区”

- 解决安装防火墙后外部网络的访问用户不能访问内部网络服务器的问题，而设立的一个非安全系统与安全系统之间的缓冲区
    - 比如邮件服务器有些东西需要对外面隔离，但有些功能又要对外界开放

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/DMZ.png)
