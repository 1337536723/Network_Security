# 非对称密钥加密 Asymmetric-Key Cryptography

## 绪论（Introduction to Public-Key Cryptography）

### 对称秘钥加密的缺陷

- 必须进行秘钥分发或密钥管理
    - 一旦秘钥被获取系统安全性无从谈起

- 秘钥要尽量避免重复使用，还要尽量随机

- 需要大量的秘钥

- 分发速度慢，延迟大

- 不能用于数字签名

注：其实对称密钥加密也不是一无是处，它**生成秘钥难**，但是**加密解密过程有可能比非对称密钥加密快**。

### 非对称密钥加密

- 在非对称加密算法中有两个秘钥
    - 公钥
    - 私钥

- 用对方的公钥加密后，对方用自己的私钥解密

- 要求
    - 看到公钥推不出私钥，并且也无法完成解密
    - 生成公钥和私钥的算法的复杂度不高
    - 加解密算法相对容易
    - 注：这里指的无法完成解密的意思并不是完全无法完成，而是在信息有效的一段时间内无法完成。<br>
    理论上，若给出足够长的时间，所有密码都可以进行暴力破解。

### 数学基础

#### 欧拉函数

欧拉函数$\varphi (n)$求的是**小于n且与n互质的正整数的个数**。这些数

比方说$\varphi (6)$等于2，因为只有1和5符合与6互质的条件。

对于**非素数**而言，欧拉函数的值就是排除因子后，剩余小于自身的素数的个数加1（因为数字1与所有数互质）。

对于**素数**而言，有以下性质：

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Euler_Function.png)

因为**素数不可分解，且与所有小于自己的正整数互质**。

#### 费马小定理

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fermat_Theorem.png)

---
##### 推论1

$$a^{p} \mod p = a$$

**证明**

$\ \ \ \ a^p \mod p$<br>
$ = (a^{p-1} \times a) \mod p$<br>
$ = [(a^{p-1} \mod p) \times (a \mod p)] \mod p$<br>
$ = [1 \times a] \mod p$<br>
$ = a$

**注**：因为a小于p，所以a模p等于a。

---

---
##### 推论2

$$a^{-1} \mod p = a^{p-2} \mod p$$

**证明**

$\ \ \ \ a^{p-2} \mod p$<br>
$ = (a^{p-1} \times a^{-1}) \mod p$<br>
$ = [(a^{p-1} \mod p) \times (a^{-1} \mod p)] \mod p$<br>
$ = [1 \times a^{-1}] \mod p$<br>
$ = a^{-1} \mod p$

---

#####应用：快速生成大素数
1. 选择一个随机整数
2. 检测 $2^{p-1} \mod p$ 是否等于1，是则成功。

#### 欧拉定理

其实就是**把费马小定理的p-1换为欧拉函数**，当n为素数时，欧拉函数的值就是n-1，此时与费马小定理是一样的。

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Euler_Theorem1.png)

这里的**relatively prime**翻译为**互质**。

- 推论（可由费马小定理的推论获得）：

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Euler_Theorem2.png)

## RSA加密系统（RSA Public-Key Cryptosystem）

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/RSA1.png)

关键在于如果p，q特别大，n很难被因式分解，
从而很难还原出$\varphi (n)$，也就很难推出d

- 因此**p,q, $\varphi (n)$不可公开**
- 但是p,q太大时，计算难度也会增大

### 注意

1. e的选取虽是**随机**，但要求满足$1 \leq e \leq \varphi (n)$，并且**e要与$\varphi (n)$这个数值互质**。
2. 计算d时，$e^{-1}$是指**e模$\varphi (n)$的乘法逆元**，而不是倒数！

### 加解密方法

1. **大整数n和选取的e组合在一起，作为公钥**发布出去。

2. **d作为私钥用于解密**。

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/RSA2.png)

### 注意

加密时，密文等于原文的e次幂**模大数n**，千万记住了！！**不是**模$\varphi (n)$。

解密时，原文等于密文的d次幂**模大数n**。

### 例子

#### 例一

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/RSA3.png)

#### 分析

n=55可以分解为质数p=5和q=11，$\varphi (n) = (p-1)(q-1) = 4 \times 10 = 40$

计算私钥d要首先计算出e模$\varphi (n)$的乘法逆元：

| 序号 | a | b | 商 | 余数 | x | y |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 | 7 | 40 | 5 | 5 | -5 | 1 |
| 2 | 7 | 5 | 1 | 2 | 6 | -1 |
| 3 | 2 | 5 | 2 | 1 | -17 | 3 |

使用扩展欧几里德定理，经过3轮辗转相除之后，可以得到乘法逆元为-17。

由此，计算出$d = (-17) \mod 40 = 23$。

然后就可以基于Square and Multiply方法来进行指数的快速求模来获取密文了~

有计算器还是用计算器呗~

####例二

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/RSA4.png)

#### 分析

题目给出很重要的信息是**两个公钥互质**，然后又提示了可以从**扩展欧几里德定理**着手。

而我们能利用的信息包括：两个公钥$e_A$和$e_B$以及大数n，还有两份密文$c_A$和$c_B$。

所以怎么用呢？ 首先，**互质的两数的最大公约数为1**。

写成Bezout等式，则有：$p \times e_A + q \times e_B = 1$

利用扩展欧几里德定理我们可以求出公钥的**乘法逆元**p和q！

然后分析一下，怎么**把原文m表达为$c_A$和$c_B$的关系式**呢？

$\ \ \ \ m \mod n$<br>
$ = m^1 \mod n$<br>
$ = m^{p \times e_A + q \times e_B} \mod n$<br>
$ = (m^{p \times e_A} \times m^{q \times e_B}) \mod n$<br>
$ = (c_A^{p} \times c_B^{q}) \mod n$

所以归纳以下，破解原文的过程就是：

1. 用扩展欧几里德定理求出公钥的乘法逆元
2. 用上面的等式计算出$m \mod n$
3. 得到原文m
    - m实际上就等于$m \mod n$，因为m就是模n得到的，所以m肯定比n小

## ElGamal加密系统（ElGamal Public-Key Cryptosystem）

参见第2章密钥管理的本原根部分。

对于一个质数p来说，当一个整数$\alpha$称为p的本原根时，其通过下式生成的a**各不相同**

$$a = \alpha^i mod \ p, \ where \ 0 \leq i \leq p-2$$

**注意区分$a$和阿尔法$\alpha$**，前者是模运算的结果，后者是质数p的本原根。

比方说2就是11的本原根：

![10](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/primitive_root.png)

可以看到$2^0$到$2^{11-2}$模11所得的结果皆不相同。

**任意一个质数都有本原根**

本原根的好处就在于，即使给出a，$\alpha$和p，**要求出i依然很难**。

因此，**在ElGamal系统中，就算把公钥a，$\alpha$和p公开，也很难解出私钥i**。

![8](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/ElGamal.png)

### 密钥生成

1. 首先选出一个大素数p
2. 然后从1到p-2中选出一个素数d作为私钥
3. 获取p的本原根e1。
4. 计算$e_2 = e_1^d \mod p$
5. 得到公钥 = ｛$e_1$，$e_2$，p｝，私钥 = d

### 加密

1. 从1到p-2的素数中选择一个随机数r。
2. 计算$C_1 = e_1^r \mod p$
3. 计算$C_2 = (P \times e_2^r) \mod p$，注意P是原文
4. 将$C_1$，$C_2$同时作为密文发送

### 解密

1. 计算$P = [C_2 \times (C_1^d)^{-1}] \mod p$


证明：

$\ \ \ \ P$<br>
$ = [C_2 \times (C_1^d)^{-1}] \mod p$<br>
$ = [(P \times e_2^r) \times e_1^{-rd}] \mod p$<br>
$ = [(P \times e_1^{rd}) \times e_1^{-rd}] \mod p$<br>
$ = P$ 得证。


### 安全性

- 本原根保证了难以解出私钥

- 在加密算法中随机选取的r使得**相同的原文可能会被加密出不同的密文**，使得从密文到原文更难被破解。

- **不能直接用于数字签名**
    - 数字签名相当于将原来的加解密过程反过来，先进行私钥的解密算法实现加密功能，<br>
    再使用公钥的加密算法实现解密功能。但在这种算法中私钥的解密算法需要多于一个参数，<br>因此不能将这个方法直接用于数字签名。

## 公钥分发（ Public-Key Distribution）

- 无控制的公钥分发
    - 一种最为简单的分发方案
        - 每个人把自己的公钥广播出去
    - 非常容易被攻击
        - 可以仿冒发送者的发送
    - 系统负担也比较大

- 基于可信中心(CA, Certificate Authority)的公钥分发
    - 所有人把自己的公钥在一个可信中心上进行备案，由可信中心完成公钥的分发

### 可信中心

- 可信中心
    - 一个可信的第三方，其核心在于发放和管理数字证书
        - 数字证书确定了公钥所有者的真实身份，包含了用户身份的信息和用户持有的公钥
        - 任何想发放自己公钥的用户，可以去认证中心申请自己的证书。<br>
        认证中心在鉴定该人的真实身份后，颁发包含用户公钥的数字证书。<br>
        其他用户只要能验证证书是真实的，并且信任颁发证书的认证中心，就可以确认用户的公钥
        - 用户可以根据自己的需要管理可信中心上的秘钥

- 基于可信中心的公钥分发
    - 用户自己生成密钥队，然后将公钥以安全的方式(比如SSL、数字
签名)传送给CA

    - CA替用户生成密钥队，然后将其以安全的方式传送给用户，CA再销毁掉用户的私钥

![9](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/public_key_distribution1.png)

![10](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/public_key_distribution2.png)

### 公钥基础设施

- 公钥基础设施实现了一整套公钥分发的功能，由以下部分组成
    - 可行中心（核心部分）：管理公钥的生成、验证和发放
    - X.509：规范证书的格式
    - LDAP目录服务器：存放公钥

层级化：

![11](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/public_key_infrastructure.png)
