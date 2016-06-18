# 传输层安全(Security at the Transport Layer)：SSL

- 应用层可以针对特定应用实现面向应用的安全协议
    - 但是不是全部的上层应用都需要专门的安全防御措施，对于安全只有着基本的需求

    - 因此提出了在传输层实现安全的方案，以解决对于基本安全保障的公共需求
        - 这样使得很多上层应用不需要再另外实现安全措施
            - 比如：浏览器https访问

### SSL(Secure Sockets Layer 安全套接层)
继任者是TLS（Transport Layer Security），用于应用层和传输层之间。

- 基于面向连接和可靠性的服务
    - TCP（UDP并不可靠）

- 为任何基于TCP的应用层协议（比如：http，ftp）提供安全服务
    - 客户机-服务器的验证
    - 数据加密解密（机密性）
    - 数据源验证（比如：数字签名）
    - 数据完整性验证

### 思考
为什么需要提供TCP/IP的验证功能？

- TCP/IP的协议本身只要我们知道地址和端口号就可以发起连接请求

- 这样很容易受到DOS攻击和通过数据包进行攻击（因为TCP/IP 无法验证发送方的身份）

- 实际上还有一种DDOS攻击，它可以变换发送方的地址

## SSL上的四个子协议(Four protocols)

- SSL record protocol
- SSL handshake protocol
    - Connection and session
    - State parameters
    - Handshake protocol
- SSL change cipher specification protocol
- SSL alert protocol

### SSL协议结构
- 为居于TCP/IP之上的两层协议
    - 可以分为两层，数据层（记录协议），控制层（其他三个协议）
    - 记录协议：提供消息源验证，数据的加密解密和数据的完整性验证
    - 第二层子协议
        - 握手协议：用于建立TCP连接
        - 改变密码规格协议：用于弃用旧会话，采用新会话时的处理
        - 告警协议：用于定义异常处理办法

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSL_protocol_structure.png)

###　SSL记录协议
- 基于面向连接和可靠性的服务(TCP)

- 提供消息源验证，数据的加密解密和数据的完整性验证

![１](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSL_record_protocol.png)

### SSL握手协议

####SSL 握手的三个目的
1. 客户端与服务器需要就一组用于保护数据的算法达成一致；
2. 它们需要确立一组由那些算法所使用的加密密钥；
3. 握手还可以选择对客户端进行认证。

#### 连接与会话
- 连接
    - 提供一种可靠的端对端的传输
    - 每一个连接都会被分配给某一个会话
    - 每一个连接自带有连接状态参数

- 会话
    - 从客户机到服务器的一种连接
    - 由握手协议进行建立
    - 内部定义有一系列的会话状态参数，对于分配到这个会话的所有连接都会共享这些会话参数（节省设置的成本）

会话也是一个端到端的连接，但它是一个最高的连接，比如两国建交，建交过后再会有后续的贸易往来。会话建立后可以建立多个连接，这些连接使用的加密算法，密钥等都是同一套，由会话确定。而会话是通过握手协议建立的。建立会话的开销很大，所以一般不会轻易扔掉。

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSL_handshake_protocol1.png)

#### 状态参数
- TCP这种可靠的连接为带状态的连接，因此会有状态参数
    - UDP为无状态的连接，因为UDP不管服务器端口是否开启，客户机仍然可以成功发送

- 每个连接都有一个连接状态参数

- 每个会话都有一个会话状态参数

##### 会话状态参数
- Session ID
    - 会话ID，也就是这个会话的序号

- Peer certificate
    - 用于互相认证的证书

- Compression method
    - 数据压缩方法

- Cipher spec
    - 数据加密解密和完整性验证（哈希）的方法

- Master secret
    - 初始的秘钥，可以将这个秘钥根据一定的秘钥扩展算法进行扩展，从而可以用于加密和哈希

- Is resumable
    - 标识可否重用（即这个会话里面的连接停掉以后这个会话还是否可以再用于以后可能的新连接）

##### 连接状态参数
- Server and client random
    - 每个连接的ID，这个ID是随机生成的

- Server/Client write MAC secret
    - TCP是双工的（服务器也可以向客户机发）
    - 对于这个连接的服务器/客户机的用于验证对方的消息验证码MAC

- Server/Client write key
    - 对于这个连接的服务器/客户机的数据加密用的秘钥

- Initialization vector
    - 由于采用CBC的模式，因此有一个IV

- Sequence number
    - 每发一个包计数器加1
    - 通过这个参数可以得知这个连接的发包数

#### 握手协议
- 握手协议将会创建一个新的会话
    - 创建过程中将会被设置为“待启用状态” (pendingstate)
    - 创建完毕以后经过“升级”(update)就会进入“启用状态” (current state)

- SSL协议中最复杂的一块
    - 包含客户机和服务器两者的相互验证（三次握手）
    - 包含客户机和服务器两者的加解密算法和哈希算法的协商
    - 必须进行的一步（跟TCP一样如果不通过握手建立连接，就无法进行消息的发送）

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSL_handshake_protocol2.png)

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSL_handshake_protocol3.png)

#### SSL握手方式
- 客户机和服务器两者的相互验证
    - 三次握手

- 客户机和服务器两者的加解密算法和哈希算法的协商
    - 服务器端验证和秘钥交换
    - 客户机端验证和秘钥交换
    - 采用的是Diffie-Hellman的算法

- 结束

##### Diffle-Hellman加密方法

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Diffle-Hellman_encry1.png)

过程：

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Diffle-Hellman_encry2.png)

### SSL改变密码规格协议

- 一个字节
- 为1时表示已经有一个新的会话建立好了，将弃用旧的会话，转为使用新的会话

### SSL告警协议

- 两种级别
    - Warning：异常，交给上层协议进行处理
    - Fatal：严重错误，直接中断连接，停传数据

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSL_alert_protocol.png)

## SSH（Secure Shell）

- SSL不是一个应用程序，而是一个协议栈，它需要和应用层进行结合

- SSH本身就是一个完整的应用程序，可以用来进行远程登录，也可以远程带加密地存储文件，<br>也提供了两个主机间安全的通信信道（可以借这个进行翻墙）
    - 墙内的客户机跟墙外的代理服务器(或者反过来)，建立好SSH连接，设置好端口转发的方式后
        - 某一台机从设置好的端口接收到的内容会自动转发到另一台机的设置好的端口上
        - 某一台机的请求也可通过转发的方式从SSH发出去并自动收回复(动态端口转发)

    - 具有静态端口转发和动态端口转发的方式

    - 由于SSH是加密的，因此中间人也看不到发送接收的实际内容

### SSH翻墙原理

- 本地(静态)端口转发
    - Home会将80端口收到的yahoo.com信息会自动转发转发到Work的9001端口

![8](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSH1.png)

- 动态端口转发
    - 某一台机的请求也可通过转发的方式从SSH发出去并自动收回复

![9](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SSH2.png)

