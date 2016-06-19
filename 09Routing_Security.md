#  路由安全(Routing Security)

## 传统TCP/IP协议的安全

### IP协议的功能
- 路由选择（最关键的功能）
    - 知道怎么转发包
- 分包与重组
    - 将大块的数据包分段
- 异常报告
    - ICMP包报告异常
- TTL
    - 清除逗留时间太长的包

### TCP协议的功能
- 通信双方建立连接与释放连接
    - 通信开始前建立连接，完成后释放连接
- 通信过程中可靠的数据传输
    - 滑动窗口协议保证不丢包、无错序
- 拥塞控制
    - 根据网络的拥塞状态调整包的发送速率

### TCP/IP协议的威胁

- 没有源IP的验证：源IP是可以伪造的
    - 可以假借他人名义发送数据包
        - 可以发送虚假的任何协议的IP包
            - 虚假的DNS回复、虚假的TCP请求、虚假的状态通告…
        - 可以进行匿名的DoS攻击
        - 可以进行匿名的病毒攻击

    - 中间路由器可以对包进行监听
        - 如果数据包进行明文传输而又没有应用网络安全的相关协议，其信息有可能会被截获

### TCP的三步握手

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/TCP_handshake.png)

SN：SYN

AN：ACK

特别注意为保证安全在第二次握手时服务器的SYN信息必须是随机的

#### SYN信息为什么要随机？

- 如果不随机可能出现危险
    - 某个客户机伪造了一个源地址的IP，伪装成另外一个主机进行TCP通信。因为SYN不是随机的，所以攻击者可以猜出服务器回复包的SYN信息，然后进行第三次握手和服务器建立了连接。

    - 可能对该区域的所有主机都造成威胁
        - 比如发一个RST包重置server（DoS攻击的一种）
            - 但这也并不能完全防范，因为可以猜测SYN包的序列号

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/TCP_attack.png)

## 路由协议的安全

### 路由协议的安全问题
- ARP （地址解析协议，Address Resolution Protocol）
    - 作用：将IP地址转换为MAC地址
    - 如果伪造了ARP包
        - 导致网关将本应转发给A节点的包转给了另外的节点
        - 可以冒充Wi-Fi的网关控制和监听数据包

- OSPF（开放最短路径优先 ，Open Shortest Path First）
    - 作用：自治域(AS)内部的路由选择
    - 如果发送一个虚假的状态通告可能导致某一网段的数据包发送受到影响

- BGP（ 边界网关协议，Border Gateway Protocol）
    - 作用：自治域之间的路由选择
    - 如果伪造一个虚假的BGP更新分组(BGP update)也会导致数据包的发送受到影响（比如发往错误的路径）

### OSPF

- OSPF的关键在于邻居发现和状态通告
    - 往指定路由器(DR)发送错误的信息
        - 链路状态通告分为路由器状态通告和网络状态通告，前者说明路由器直接相连的网络和路由器的信息，后者说明网络直接相连的网络和路由器的信息
        - 因为网络链路状态通告会由指定路由器进行收集后再进行发送，如果某一路由器向指定路由器发送了错误的状态通告可能导致包的转发路径改变

    - OSPF的状态通告是有加密的
        - 但很遗憾，用的是MD5(基于RC4的加密方法)，还是有破解的可能
        - OSPF有一定的措施防范这个问题
            - fight back机制：当我们恶意修改某一状态通告时，这个通告会泛洪(flooding)到全网，这时候本该发出这个状态通告的路由器就会发现问题，从而重新发送链路状态通告来更正错误
            - 每隔一段时间都会更新一次状态通告，使得攻击的效果不可持续
            - 但对于邻居发现信息并不会发给全网，因此还是存在一定漏洞，攻击者可以冒充成邻居来进行攻击

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/OSPF_attack.png)

### BGP

- BGP的状态通告信息不进行验证
    - 因此出现错误的状态通告信息也不会被发现
    - 为DoS攻击，数据包的监听和伪造带来可能
        - DoS：将数据包引导到了某个带有防火墙过滤规则的路由器上
        - 数据包的监听和伪造：可以通过更改包的转发路径实现

- 解决办法
    - RPKI：当RIR(Regional Internet Registry，地区性 Internet注册机构)往每个自治域分配可用IP地址段的同时发放许可信息。许可信息需要伴随BGP的状态通告信息进行发送时状态通告信息才是有效的
    - SBGP：转发状态通告的每个路由器都要进行记录
        - 这样就知道了发送信息的真正源头
        - 这样实现对于性能会存在影响（因为要把经过的所有信息记下来）

### 域名系统DNS
- 域名系统(DNS, domain name system)的作用在于将域名(比如mail.sysu.edu.cn)转换为IP地址
    - 转换遵循层级结构
    - DNS有两种方式完成转换过程
        - 迭代的方式
        - 递归的方式

#### 迭代方式

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/DNS_structure1.png)

Local DNS要知道www.cs.stanford.edu的IP，会先问edu的DNS服务器，edu的DNS服务器会告诉Local DNS下一步去问stanford.edu的DNS服务器，然后Local DNS收到指示后会继续问下去

#### 递归方式

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/DNS_structure2.png)

Local DNS要知道www.cs.stanford.edu的IP，会先问edu的DNS服务器，edu的DNS服务器会自动去问stanford.edu的DNS服务器，如此递归下去最后再将结果返回给local DNS

递归方式看上去很美，但是给根DNS服务器带来了非常重的负荷，并不现实，实现上用的基本都是迭代方式。

#### 域名缓存（cache）

- 系统会自动将之前的查询结果（不论是正确的还是不正确的）缓存下来，以节省查询的时间
- 查询的内容有一定的生存期

### DNS的安全问题

#### 域名污染(DNS cache poisoning)

- 本来域名a对应IP为A的，结果local DNS的域名映射受到污染使得域名a对应到了另外一个IP上面，从而每次我们以为访问的是域名a，实际上并不是

- 常见于免费Wi-Fi中，因为域名映射的信息需要经过Wi-Fi的网关，而Wi-Fi的网关可以返回一个错误的域名映射（本地DNS攻击）

- 还有一种伪造DNS包的方法，前提是要猜中随机生成的Query ID（远程DNS攻击）
    - 但Query ID只有16位因此还是很可能猜中

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/DNS_packet.png)

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/DNS_attack1.png)

##### 解决办法
- 将query ID的位数变大
    - 不太可行，因为需要更改整个数据包的结构和解析方式

- 在进行TCP连接进行数据交互时任意选取端口号，使得攻击者一开始还要猜使用的是哪个端口（可行）

- 发送两次随机产生的query ID
    - 会造成DNS系统负荷的增大（增大一倍）

#### 域名重绑定(DNS Rebinding Attack)

- 过程
    - 首先用户访问了一个不安全的域名A，A会返回给local DNS一个TTL非常小(比如为0)的IP，此时浏览器就会访问这个IP，把所有的页面代码抓取回来显示在浏览器上
    - 但用户没想到的是抓取回来的可能不仅仅是一段html，在html里面可能隐含有非法js文件的索引或者一些有漏洞的html标签(比如：img)，这些文件或代码会被自动执行。在这些文件中再一次访问了不安全的域名A
    - 由于之前域名A对应的IP并没有缓存下来（因为设置了TTL为0，不会继续缓存），因此会再次去询问DNS，结果这次返回的是防火墙内部的主机B的IP，从而该用户的电脑会自动获取主机B里面的隐私数据
    - 在用户提交表单给所谓的域名A的时候可能夹杂有主机B里面的隐私数据，攻击者就可以通过这种方式盗用公司机密之类的数据了。

- 这种攻击往往始料未及，而且很难发现

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/DNS_attack2.png)

##### 解决办法
- 不允许在域名访问时切换IP
    - 不太可行，因为有时确实有更换IP的需求，比如外部索引框架(frame)或图片(img)

- 在上层认证访问者
    - 浏览器对于一些上了黑名单的域名会自动拒绝访问，弹出警告信息

- 在防火墙端做好防范
    - 拒绝外部DNS返回内部IP
