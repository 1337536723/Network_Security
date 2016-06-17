# 数学基础 Mathematics Basic

## 模运算

给定整数b大于0，对任意整数a而言，必然存在两个唯一的整数q和r使得：

$$a = qb + r$$

其中 $q = \lfloor a/b \rfloor$，$0 \leq r \leq b$。

定义模运算：

$$ r = a \mod b$$

### 余集 $Z_n$

定义余集$Z_n$为包含元素0~n-1的集合。比方说：

$Z_2 = \{0,1\}$

$Z_6 = \{0,1,2,3,4,5\}$

### 模运算

假设整数n>1，定义：

$$x \oplus _n y = (x+y) \mod n$$
$$x \ominus _n y = (x-y) \mod n$$
$$x \otimes _n y = (x \times y) \mod n$$

### 模运算的性质

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/modular_operation.png)

### 其他

1. **在余集$Z_n$中运算直接采用$+,-,\times$来代替模运算符号。**

2. **特别地，$\odot$符号表示加法和乘法两种运算**

3. **异或可以认为是模2加法的运算结果**

    - 用 $\oplus$ 表示异或，也即模2加法 $ \oplus _2$
    - 对任何x属于0或1，有 $ x \oplus x = 0 $
    - 若 $x \oplus y = z$ 则 $x = y \oplus z$

## 群，环，域（Group, Ring, Field）

群，环，域都是抽象代数的概念，可以理解为一种特定的集合，集合上定义着特殊的运算。

### 群

一般定义为$\{G,\cdot\}$，其中$\cdot$是一种二元操作，满足：

- (A1) 封闭 Closure: 若a和b属于G，则$a \cdot b$也属于G。

- (A2) 结合律 Associative: 对所有a，b，c属于G，有$a \cdot (b \cdot c) = (a \cdot b) ~ c$ 。

- (A3) 单位元 Identity element: 对于G中任意元素a，G中存在单位元e使得 $a \cdot e = e \cdot a = a$。

- (A4) 逆元 Inverse element: 对于G中任意元素a, G中存在逆元 $a'$ 使得 $a \cdot a' = a' \cdot a = e$。

- (A5) 交换律 Commutative: 对所有a，b属于G，有$a \cdot b = b \cdot a$

**注：A5可选，满足A5的群称为是abelian（广义）的。**

例子：$Z_{n*}$就是一个乘法群，它表示的是小于n且与n互质的整数集，是余集$Z_{n}$的一个子集。

### 环

一般定义为$\{R,+,\times\}$，包含加法（addition）和乘法（multiplication）两种二元操作，满足：

- 群的A1-A5公理。

- (M1) 乘法闭包:  若a和b属于R，则ab也属于R。

- (M2) 乘法结合律: 对所有a，b，c属于R，有$a(bc) = (ab)c$

- (M3) 乘法分配律: 对所有a，b，c属于R，有$a(b + c) = ab + ac$ 以及 $(a + b)c = ac + bc$

若满足M4，则称该环是commutative（可交换）的，也称交换环。

- (M4) 乘法交换律： 对所有a，b属于R，有$ab = ba$

若再满足M5-M6，则称该环是integral domain（整环）。

- (M5) 乘法单位元： 对R中任意元素a，R中存在元素1使得 $a1 = 1a = a$

- (M6) 无零因子： 对所有a，b属于R，若$ab = 0$，则a，b之中的一个为0。

简洁地说，**环就是一个加减乘都封闭的的集合。**

### 域

一般定义为$\{F,+,\times\}$，包含加法（addition）和乘法（multiplication）两种二元操作，满足：

- 群的A1-A5公理，环的M1-M6公理

- （M7） 乘法逆元： 对于F中除0以外的任意元素a, G中存在逆元 $a^{-1}$ 使得 $aa^{-1} = a^{-1}a = 1$。

简洁地说，**域就是一个加减乘除都封闭的的集合。** 这里除法的定义是 $a/b = ab^{-1}$。

### 注意

以上所说的加法和乘法**并非算术上的加法和乘法**！举个例子，可以是模运算中的加法和乘法。

### 有限域

有限域，又称**伽罗华域**。一般定义为$GF(p^n)$，这里**p为素数，且该有限域中元素的个数必定为$p^n$**。虽然也存在无限域，但在密码学中我们只关注有限域。

前面谈到的余集$Z_n$，当n是素数时，也记作$Z_p$。这种**素数的余数集就是一个有限域**。

举个例子：$GF(7^1)$，也即$Z_7$：

$$Z_7 = \{0,1,2,3,4,5,6\}$$

M7针对的是**0以外**所有元素，可以很容易算得，在这个余集中，1的乘法逆元是其本身，2和4互为乘法逆元，3和5互为乘法逆元，6的乘法逆元是其本身。所有元素的乘法逆元都能在该存在于该集合中，因此满足作为域的要求，又因为该集合中元素个数是有限的，所以$Z_7$属于伽罗华域。

## 指数的快速求模（Fast Exponentiation）

###  Square and Multiply方法

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Exponentiation1.png)

这个公式也就是说，要对一个高次幂求模，可以分解为若干低次幂求模的乘积，然后再求模，得到的结果是一致的。

比如，求 $11^{207} \mod 13$

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Exponentiation2.png)

最后是 $(9\times3\times9\times3\times4\times11) \mod 13 = 5$

再比如，求 $23^{237} \mod 19$

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Exponentiation3.png)

在分解到最小以后，先计算最小分解项的模，然后像对雪球一样可以逐步把高次幂分解项的模也算出来，最后完成整个求模运算。这就**全靠细心**了..

## 快速求逆（Fast Inverse）

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Inverse1.png)

在模运算中，求逆是指求**乘法逆元**，即上式中的$a^{-1}$，**a的逆满足与a相乘所得的积模b等于1**。有时我们需要计算逆，这时我们就需要找出一个N使其能满足第二个式子，得到**整数逆**。当a非常大时，要这样**逐个N尝试的话效率就变得很低**了。

###欧几里得定理(Euclidean Theorem)

欧几里德算法，又称**辗转相除法**，用于**计算两个整数a,b的最大公约数gcd(a,b)**。

####　核心： $gcd(a,b)=gcd(b, a \mod b)$

证明：

假设a是两数中较大的那个数，

a可以表示为 $a = kb + r$，余数 $r = a \mod b$

> 假设c是a,b的一个公约数，则有a整除c, b整除c，<br>
而r = a - kb，因此r整除c<br>
因此c是(b,a mod b)的公约数

> 假设c是(b,a mod b)的公约数，则有b整除c, r整除c，<br>
又因为a可以表示为a = kb +r，所以a也整除c<br>
因此，c也是(a,b)的公约数

因此，(a,b)和(b,a mod b)的公约数是一样的，其最大公约数也必然相等，得证

以上就是**辗转相除求gcd**的理论基础，每次**将大数转换为模小数所得的余数**。

到最后，大数除以小数**余数为0时就终止**，此时的小数就是最开始两数a和b的最大公约数。

###扩展欧几里得定理(Extended Euclidean Theorem)

扩展欧几里德算法则用于**求解模线性方程及方程组**。

在已知a, b求解一组x，y，使它们满足等式： $ax+by = c$，其中a,b,c均为整数

法国数学家Bezout证明当且仅当c是a和b**最大公约数的整数倍**时有整数解x,y。

- 并且，如果x,y是其中的一组解，那么其他的解满足(x+kq,y-kp) (其中p为a/c，q为b/c，k为整数)

- 可以推出，若a和b互质，则x为a模b的逆(等式左右两边模b)

####　证明： a和b互质时，x为a模b的逆

因为c必然是gcd(a, b)的整数倍，假设是1倍，则有：

$$ax + by = c = gcd(a, b)$$

对该式左右同时模b，因为左边by模b是0，所以只剩下ax这一项。

右边因为a和b互质，所以gcd是1，1模b还是1，于是有：

$$ax \mod b = 1$$

由此可得x是a模b的逆，同理我们也可推得y是b模a的逆。

#### 如何运用到求乘法逆元？

在上面的推导中，我们证明了x和y分别是互质的a和b的逆，接下来就是要**计算x和y的值**。

先看辗转相除法第一轮

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Inverse3.png)

再看辗转相除法第二轮

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Inverse4.png)

最后看个普遍的情况

![8](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Inverse5.png)

借助欧几里德定理来求乘法逆元的核心就是：**借助辗转相除不断地更新x和y**。

最终，新的一轮相除中，若**余数为1则终止**，此时所得的x和y就是所求a和b的乘法逆元。

**注意：每轮除法都是大数被除。**

**注意：更新x和y要按公式来！**

$$ x_i = x_{i-2} - N_i \times x_{i-1}$$

$$ y_i = y_{i-2} - N_i \times y_{i-1}$$

####特别特别要注意

$x_0$，$y_{-1}$等于1，而$x_{-1}$，$y_0$等于0。

#### 例子

![9](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Inverse6.png)

5模7的逆就是最后一轮除法的x，也即3。

![10](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Inverse7.png)

8模77的逆就是最后一轮除法的x，也即29。

## 快速因式分解（Fast Factorization Methods）

- 对N进行因式分解最简单的办法是从1开始一直试
到sqrt(N)

- 一个快一点的方法是Fermat方法

![11](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Factorization1.png)

根号N肯定落在因子p和q之间，而p和q的均值会比根号N大一些。

### Fermat方法

假设p和q的差值为2x，A为p和q的均值。

则p=A-x, q=A+x，因此：

![12](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/Fast_Factorization2.png)

我们的目标转换为**先求出x，继而求出因子p和q**。

怎么做呢？

1. 要算出x**首先要知道均值A**，又因为A比根号N大一些，我们可以**从根号N开始枚举A**。

2. 由于A和根号N的值离得较近，所以我们尝试的次数远比从1枚举到根号N少多了。

3. 当**枚举到一个A能够求出整数的x**时，那就成功了。

4. 获取x后，就可以计算出p=A-x, q=A+x，得到因式分解的结果了。


