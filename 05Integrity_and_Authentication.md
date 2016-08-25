# 完整性与验证 Integrity and Authentication

## 完整性（integrity）

- 一般用于验证内容是否完整或者发送方是否正确

- 一般情况是，将原文和与原文密切相关的一段特殊数据进行发送

    - 这段特殊数据可能是一段原文的加密也可能是原文的哈希，称为认证函数（authentication function）
    - 注释：这里发送的东西只是为了保证完整性用的，如果还要保证安全性还得加上加解密算法

- 接收方收取后再通过同样的认证函数得到新的特殊数据然后和原来的那段数据做匹配，如果匹配成功则验证成功，否则验证失败

## 验证函数（Authentication Functions）

- 可以是一个加密函数（也就是将原文进行加密以后的密文），这称为Encryption transformation

- 也可以是一个哈希函数（也就是将原文进行哈希映射后得到的值）
    - 哈希函数可能是与秘钥相关的，也可能是与秘钥无关的
    - 一般来说Hash function一般指秘钥无关的，Keyed hash function一般指秘钥相关的

## 通过加解密进行验证

- 发送方发送一段加密的文本到接收方，接收方再解出来

- 单靠加密解密是否可以保证完整性
    - 也就是密文被篡改以后，接收方能不能察觉
    - 有可能可以，有可能不行
    - 如果是一段自然语言有可能被发现（因为原文是有意义的，如果解密出的无意义就说明被篡改了）
    - 也有可能发现不了，比如将全文进行替换
    - 如果是一段非自然语言（比如密码）很有可能发现不了
    - 学术一点来讲就是在语言为有限集合的时候可能可以，如果语言为无限集合就不可以

## 哈希与哈希函数（Hash and Keyed Hash Functions）

### 通过哈希函数进行验证

- 一种最简单的方法
    - 发送方发送原文和原文的哈希值（要保证安全性则要在发送前对这一个整体进行加密）
    - 接收方接收新的原文和原文的哈希值（如果有加密，则需要进行解密）
    - 接收方完成原文的哈希值的前后比对，如果匹配则完成验证
        - 一般来说如果哈希函数设计得好完整性这样的验证是很有效的（简单但低冲突）
        - 只加密哈希函数也比完全不加密要好，因为完全不加密的话，有可能原文和哈希函数值都被换掉了

### 对哈希函数的要求

- 不同的原文映射到同个值的概率极低，一般这样的函数值是256bits的，而且均匀分布

- 单向性（one-way property）：给定哈希值无法在有效时间内反算出原文

- 弱冲突性（weak collision resistance property）：给定某一文本，无法在有效时间算出另一个不同但具有同样哈希值的文本

- 强冲突性（strong collision resistance property）：无法在有效时间算出任意两对哈希等值的文本

**一定要注意好若冲突性和强冲突性的区别！！**

### 拒绝否认（Authentication with Nonrepudiation）

- A在给B发送消息的时候可以用A的私钥对文本内容进行加密

- B收到以后就可以用A的公钥进行解密，只要解出来A就**无法否认自己发送过这个消息**（因为A的私钥只有A自己有）

<img src="http://latex.codecogs.com/gif.latex?Alice \rightarrow m \parallel D_{k_{dA}}[h(m)] \rightarrow Bob" />

- 为了保证安全性，有可能经过一层加密才发送

<img src="http://latex.codecogs.com/gif.latex?Alice \rightarrow E_k(m \parallel D_{k_{dA}}[h(m)]) \rightarrow Bob" />

**注意，这里的<img src="http://latex.codecogs.com/gif.latex?\parallel" /> 符号表示两边的内容连在一起（合并为一个整体）**

### 构造哈希值的方法

给定块加密（<img src="http://latex.codecogs.com/gif.latex?M" /> ,<img src="http://latex.codecogs.com/gif.latex?C" /> ,<img src="http://latex.codecogs.com/gif.latex?K" /> ,<img src="http://latex.codecogs.com/gif.latex?E_k" /> ,<img src="http://latex.codecogs.com/gif.latex?D_k" /> ），块大小为n bits，加密算法<img src="http://latex.codecogs.com/gif.latex?E_k" /> 把n bits的块映射为另一个n bits的块，每一个key的长度为l bit。

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/construct_hash_value1.png)

#### 流程图

**The Merkle-Damgard paradigm**

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/construct_hash_value2.png)

**所有哈希系统的基准都是Merkle-Damgard范式**，整体看作一个大H函数，也即哈希函数。内部的小h函数称为压缩函数(compression function)。大H的抗冲突性（collision resistance）取决于小h的抗冲突性。正面推导比较困难，但我们可以反过来证明，**只要大H有抗冲突性，则小h必定有抗冲突性**。

### 秘钥相关的哈希函数

- 其实也是个哈希函数，不过跟秘钥相关

- 普通的哈希函数是公开的算法，没用到密钥，所以随便一个人都可以用自己的消息计算一个哈希值发给你，你检查后还是合法的消息。但如果你跟对方约定好一个密钥，再计算哈希值，截取的人不知道你们的密钥就不能计算出合法的哈希值。

- 带秘钥的哈希函数比不带秘钥的哈希函数要相对安全

- 对于任意的x，通过带秘钥的哈希函数算出的值称为哈希值或者消息认证码（MAC）

#### 使用方法

- 和哈希函数的使用方法类似。首先算出一个hash值和原文一起发送，接收方收到以后再算一个新的hash值和原来那个进行比对。匹配成功就说明验证成功，否则验证失败

- 在这种情况下因为哈希函数是带秘钥的，因此并不像普通的哈希函数一样进行原文和哈希同时替换那样容易。在带秘钥的哈希函数中，哈希值只能靠猜，因此对于一个n bits的哈希函数来说，任意两对文本哈希值相同的概率仅为<img src="http://latex.codecogs.com/gif.latex?\frac{1}{2^n}" />

- 但不管怎么说这种情况还是不保证机密性（因为原文并没有加密…），因此**如需保证机密性还需要对原文进行加密**

- 构造哈希值的方法和不带秘钥哈希的构造方法相类似

### 例题

#### Question

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/construct_hash_value3.png)

#### Answer

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/construct_hash_value4.png)

显然，只是简单地把密钥和哈希值异或是不能保证安全性的，因为只要哈希算法公开，发送的原文又没有加密，那么任何人都可以算出原文对应的哈希值。然后根据异或的性质，只要对原文和原文的哈希值进行异或就可以得到密钥了。得到密钥后便可以仿造任何信息。

### HMAC

HMAC就是一种密钥相关的哈希函数。

- h 是哈希函数, 哈希值长度为n bits。
- b 是一个选定的正整数且b整除8。
- K 是密钥，长度最多为b bits。
- <img src="http://latex.codecogs.com/gif.latex?\overline K" />  是K左边补满0，使得长度为b bits。
- 输入填充ipad和输出填充opad
    - ipad = 00110110 重复 b/8 次（使得长度为b bits）。
    - opad = 01011100 重复 b/8 次（使得长度为b bits）。

<img src="http://latex.codecogs.com/gif.latex?HMAC_K(m) = h[(\overline K \oplus opad) \parallel h[(\overline K \oplus ipad) \parallel m]]" />

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/HMAC.png)

## 安全性哈希算法（ The Secure Hash Algorithm ）

### SHA1

- S1：比特填充（Append padding bits）
    - 目的：对输入的数据进行填充，使得数据位长度对512求余的结果为448
    - 填充方法：1+若干个0
    - 为什么要变成数据位长度对512求余的结果为448？

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SHA1.png)

- S2：加入原始数据长度（Append length of the original message）
    - 目的：用64位说明原始数据长度，使得总长度能被512整除

- S3：初始化MD缓存（ Initialize MD buffer ）
    - 一个160-bit的buffer用于保存哈希函数的中间和最终结果。
    - 这个buffer一般表示为5个32-bit的寄存器 A,B,C,D,E。

- S4：对每个512位的块进行处理（Process padded message in 512-bit blocks）
    - 其中用到很多位运算，位运算的好处在于运算速度快

## 数字签名（ Digital Signature ）

- 数字签名需要实现两方面的功能
    - 验证发送方的身份
    - 使得发送方不可否认曾经发出的信息（Nonrepudiation）

- 数字签名的要求和之前用于加密的要求大致相同
    - 产生数字签名的方式要尽量简单
    - 验证数字签名的方式要相对简单
    - 在短时间内无法破解数字签名
    - 运算的时间空间复杂度要尽量小

### 思考

- 这样的发送方式是否能满足数字签名的要求？

<img src="http://latex.codecogs.com/gif.latex?Alice \rightarrow E_{k2}[m \parallel h_{k1}(m)] \rightarrow Bob" />

- 答案是不能
    - 这种发送方式中k1和k2确实只有Alice和Bob知道，确实可以保证一定程度上的机密性（confidentiality）和完整性（ integrity ），也就是说Bob可以验证发送方的身份（也就是可以验证这条消息是不是Alice发出来的）

    - 但是Alice是可以否认自己发送过的消息的，因为存在Bob伪造消息的可能（因为Bob是知道<img src="http://latex.codecogs.com/gif.latex?h_{k1}" /> 的…）

    - 这也就是为什么数字签名必须使用Alice的私钥加密

<img src="http://latex.codecogs.com/gif.latex?Alice \rightarrow E_{k}(m \parallel D_{k_{dA}}[h(m)]) \rightarrow Bob" />

### 数字签名方法

- 发送方用自己的私钥进行加密，传到接收方以后就可以用发送方的公钥进行解密，只要解出来发送方就无法否认自己发送过这个消息（因为发送方的私钥只有发送方自己有）

- 这样对于RSA这种算法来说可以直接应用到数字签名上，因为RSA的解密函数只需要一个参数传入（在非对称密钥加密算法里面RSA是密文传入，输出原文；在数字签名里面是原文传入输出密文，由于加密算法和解密算法是反运算，因此先通过加密算法再通过解密算法和先通过解密算法再通过加密算法出来的结果是一样的）

### 数字签名方法（cont）

- 但是这对于ElGamal加密算法来说有个问题，它的解密函数里面有两个参数要传入…因此ElGamal算法不能直接应用在数字签名方法里面

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/cont.png)
