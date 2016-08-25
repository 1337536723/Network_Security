# 应用层安全 Security at the Application Layer：邮件系统安全(Electronic Mail Security)

## 电子邮件安全要素(Email security issues)

### 电子邮件安全目标
- 消息的机密性
    - 只有发送端和接收端能看到明文的消息，而在之间的传输过程都应该是加密后再进行传输

- 拒绝否认
    - 接收方收到邮件以后发送方不可否认本邮件没有发过

- 消息的完整性
    - 如果消息在中途被篡改应该要被发现

- 这些要素都应该封装在一个模块里面，而这个模块应该是与系统、处理器等硬件无关的

### 电子邮箱协议
- PGP：Pretty Good Privacy
- S/MIME：Secure/Multipurpose Internet Mail Extension

## PGP协议(PGP Protocol)

### PGP服务
- 拒绝否认和完整性验证
    - DSS/SHA或RSA/SHA

- 消息的机密性
    - 消息加密：通过CAST/IDEA/3DES（对称秘钥加密）进行
    - 密钥加密：通过ElGamal/RSA（非对称密钥加密）进行

- 压缩
    - ZIP

- 邮箱兼容性
    - Radix-64：将ASKII以外的字符做映射

- 分段
    - 如果邮件内容过长不能放在一段以内

### 完整性和数字签名

<img src="http://latex.codecogs.com/gif.latex?Z" /> 表示压缩，<img src="http://latex.codecogs.com/gif.latex?Z^{-1}" /> 表示解压。<img src="http://latex.codecogs.com/gif.latex?H" /> 表示哈希（比如使用SHA1），<img src="http://latex.codecogs.com/gif.latex?\Vert" /> 表示连接两段信息。

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/PGP1.png)

输入原文M，首先使用SHA1生成哈希码，然后采用私钥进行签名（基于RSA），然后把得到的签名拼接上原文再发送。接收者收到后使用公钥解密，恢复出哈希码和原文，对原文使用SHA1生成哈希码然后进行比对，如果和发来的哈希码一致就认为是可行的并且接收。

### 机密性

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/PGP2.png)

输入原文M，采用一个随机的128bit数字作为会话密钥，这个密钥仅用于该信息。 使用会话密钥对原文进行加密（对称密钥加密），并且会话密钥会用接收者的公钥进行加密（RSA算法）然后拼接到加密后的原文后面。 接收者收到后用自己的私钥解密出会话密钥，然后采用会话密钥解密出原文。

### 机密性+完整性+数字签名

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/PGP3.png)

### 关于压缩

在书本596-597页。

- 压缩的直接好处：节省传送邮件的带宽和存储文件的空间

- 压缩的位置：
    - 为什么在压缩后再进行加密？
        - 这是因为压缩后信息的冗余性比原文少<br>这样黑客要进行破解就更加困难
    - 为什么在签名后再进行压缩？
        - 这样在需要进一步验证时我们只需要存储解压的信息和签名<br>而无须同时存储压缩后的版本。


### PGP中的秘钥使用
- 会话秘钥
    - 用于进行一次会话的随机生成的秘钥
    - 一般通过对称秘钥加密算法进行实现

- 公钥与私钥
    - 不仅要保存自己的公钥私钥对，还要保存接收方的公钥
    - 同时，一个人可能也有多个公钥私钥对

- 基于邮箱密码的秘钥

### 发送中的公钥私钥问题
- 在接收方拥有多个公钥私钥对的时候，接收方在收到内容以后怎么知道用哪个去解？
    - 方法1：发送方将加密用的公钥和加密后的文本一同发送
        - 缺点：浪费资源（因为公钥往往很长…）
    - 方法2：对于每个公钥私钥对都分配一个ID，然后用户也分配一个对应的ID，发送两值相减的结果
        - 缺点：发送接收方都要保存这样一张诺大的映射表
    - 方法3：发送公钥模除2^64后的结果
        - 缺点：可能会有一定的冲突（但实际上概率极小）

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/PGP4.png)

- 时间戳：避免重放等攻击
- 摘要：进行数字签名验证的时候没必要对于整个原文进行
    - 摘要的前两个字节（Leading two octets of message digest）的功能<br>在于减少密钥使用错误的尝试成本：解出摘要以后先和这前两个字节进行比较，如果不对就不用算原文的Hash了

### 秘钥环
- 在进行完整性数字签名验证的时候用到一个秘钥ID，然后进行机密性的时候又有另外一套秘钥ID

- 秘钥环提供了一套高效保管的方案

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Private-Key_Ring.png)

- Pi一般是邮箱的密码，输入密码后就可以把私钥解出来(也就是在私钥环里面的私钥是通过邮箱的密码加密的)

- User i一般指邮箱的邮箱号

- 这段内容只有邮箱拥有者自身可以见到

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Public-Key_Ring.png)

- User i一般指邮箱的邮箱号

- 这部分内容可以公开

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/PGP5.png)

当用户使用邮箱服务时，它会有一个ID，根据这个ID可以从私钥环中取出加密过的私钥，用户登录时输入密码，这个密码哈希后可以用于解密用户的私钥。得到私钥以后就可以用于对发出的邮件进行签名了。同时密钥的ID也会提取出来拼接上。 然后在加密的时候就要从公钥环提取出接收方的公钥，采用会话密钥对签名后的信息加密，然后利用公钥对会话密钥加密再拼接上密钥ID然后发送。
