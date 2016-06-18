# 网络层安全(Security at the Network Layer)：IPSec

- 网络层主要起到选路的功能

- 传输层的安全协议SSL并不足以解决所有问题
    - SSL基于状态，因此对于无状态的连接UDP(比如视频)没办法保证安全
        - 基于状态的好处在于安全性更高(因为可以不同的场景切换不同的状态)，而网络层对于数据包是同等对待的

    - SSL的算法稍微复杂效率不高
        - 网络层可将算法进行硬件实现，且尽量精简了算法(比如：只需要在端系统间实现秘钥交换，不需要中间每个路由器都进行秘钥交换)

    - SSL可能会导致调用端口的变化(https有自己专门的端口，上层应用必须遵循，所以如果想用SSL就要修改程序)
        - 而网络层安全对于应用层来说是透明的，应用不需要理会

- IP包在传输时通常是明文传输的，黑客很容易看到数据内容，伪造源和目标地址，修改内容，重放数据包等等。

- 因此需要额外补上一个协议来保证安全
    - IPSec提供了安全性，完整性的一系列的保障

## IPSec结构

- 两个数据库：
    - 安全策略数据库（Security Policy Database）
    - 安全关联数据库（Security Association Database）

- 两个通信协议：
    - 验证文件头（Authentication Header）
    - 封装安全（Encapsulating Security Payload）

- 两种模式：
    - 传输模式（Transport Mode）
    - 隧道模式（Tunnel Mode）

- 一个秘钥交换管理协议：
    - 互联网秘钥交换（Internet Key Exchange）

## IPSec的两个数据库(Two Database)

### 安全策略数据库（SPD）

根据数据流的源地址，目的地址，上层协议，端口号等（这些信息统称为traffic flow）将其唯一地映射到一个SA上，使得所有这样的数据流都采用同一套加密方式。

- 对于收到的包有一个接收的安全策略数据库
    - 对于发出的包进行查询，确定其操作
        - 扔掉（drop）：比如异常的包
        - 正常传输（bypass）：不经过IPSec，就当成一个普通的包进行传输
        - 应用（apply）：通过IPSec的一系列处理机制
            - 查询安全关联数据库（SAD）确定这个包的处理方法
                - 查到就按照对应的方法进行处理
                - 没查到就需要通过秘钥交换来问怎么处理

    - 对于收到的包进行查询，确定其操作
        - 扔掉（drop）：比如说列入黑名单的包
        - 正常传输（bypass）：不经过IPSec，就当成一个普通的包进行传输
        - 应用（apply）：通过IPSec的一系列处理机制
        - 查询安全关联数据库（SAD）确定这个包的处理方法
            - 查到就按照对应的方法进行处理
            - 没查到就扔掉（很有可能安全证书已经过期）

#### 对于发出包的处理

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SPD1.png)

#### 对于接收包的处理

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SPD2.png)

### 安全关联（security association）

保存各类数据流的加密方式，协议内容等等。数据流在不同时间可能用的是不同的SA，也就是说SA是有lifetime限制的，但手动配置的就没有。

- SA主要提供安全服务所需的参数
    - 按我的理解也就是说明往某个地址发出的包采用什么样的安全服务或是收到某个地址发来的包采用什么样的安全服务，从而可以对数据包进行合适的处理

    - 因此SA是单向的

    - 所有SA的数据会被存放在安全关联数据库（SA Database）里面方便管理，通过查询数据库就可以得到对应的加解密和验证方法

    - 主要是三个参数进行索引查询
        - Security parameter index：索引值，会伴随着数据包进行发送
        - Destination address：发出时说明下一个处理这个IPSec包的地址是什么，收到时查看是通过什么源地址发出来的
        - Protocol：AH协议还是ESP协议

    - 查询结果包括：AH或ESP协议进行完整性验证所用的秘钥，ESP协议进行加解密所用的秘钥，模式和生存期等一系列的参数

### 架构

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Database_Structure.png)

## IPSec的两个安全协议(Two Security Protocols)

### 验证文件头（AH）协议

- 不提供加密功能，只提供数据完整性和数据源验证的功能

- 可能可以做到拒绝否认（看具体用的是什么加密方法）和防范重放攻击的功能（看有没有带上时间信息）

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_AH.png)

### 封装安全（ESP）协议

- 提供了加密的功能

- 也可以进行完整性验证

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_ESP.png)

- 如何处理ESP协议包
    - 先对照安全关联数据库（security association database）以获取解密的方法以及哈希的方法
    - 然后进行完整性验证
    - 最后再进行解密

- 为什么包头不加密，包尾加密
    - 包头里面有SPI（security parameter index），加密以后就不知道用什么解密方法去解密了
    - 而包尾还是有一些重要信息（比如padding和padding的长度）

## IPSec的两种模式(Two Modes)

### 传输模式（Transport Mode）

- Payload是一段用于传输的数据
    - 整段payload经过IPSec的AH/ESP协议处理
    - 不保护IP的头，只保护里面传输的数据
        - 也就是IP的头是明文发送的

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Transport_Mode.png)

### 隧道模式（Tunnel Mode）

- Payload是一个完整的IP包
    - 整段payload经过IPSec的AH/ESP协议处理
    - 不仅保护里面传输的数据，还保护IP的头
        - 因此可以用来翻墙（因为真正访问的地址加密在payload里面，防火墙只是看到了外面的地址）

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Tunnel_Mode1.png)

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Tunnel_Mode2.png)

#### 两种翻墙技术的不同
- VPN的设置是全局的，即电脑一旦挂上VPN，所有的联网程序都将自动使用VPN(网络层)；而建立好SSH隧道后，需要程序设定使用隧道才会使用隧道联网

- VPN的搭建难度比SSH大，但是SSH比较容易防(因此VPN翻墙一般需要掏钱)
    - 有办法做到拒绝和墙外进行SSH连接

## 因特网秘钥交换(Internet Key Exchange(IKE))

SA中的加解密算法的秘钥怎么来？

![8](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Internet_Key_Exchange.png)
