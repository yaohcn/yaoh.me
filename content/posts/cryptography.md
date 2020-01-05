---
title: "密码学总结"
date: 2020-01-05T19:47:52+08:00
draft: false
---
### 密码学总结
#### HASH
由于用途的不同，hash在数据结构中的含义和密码学中的含义并不相同。  
抗碰撞能力：对于任意两个不同的数据块，其hash值相同的可能性极小；对于一个给定的数据块，找到和它hash值相同的数据块极为困难。  
抗篡改能力：对于一个数据块，哪怕只改动其一个比特位，其hash值的改动也会非常大。   

在用到hash进行管理的数据结构中，比如hashmap，hash值（key）存在的目的是加速键值对的查找，key的作用是为了将元素适当地放在各个桶里，对于抗碰撞的要求没有那么高。换句话说，hash出来的key，只要保证value大致均匀的放在不同的桶里就可以了。但整个算法的set性能，直接与hash值产生的速度有关，所以这时候的hash值的产生速度就尤为重要。  

在密码学中，hash算法的作用主要是用于消息摘要和签名，换句话说，它主要用于对整个消息的完整性进行校验。举个例子，我们登陆知乎的时候都需要输入密码，那么知乎如果明文保存这个密码，那么黑客就很容易窃取大家的密码来登陆，特别不安全。那么知乎就想到了一个方法，使用hash算法生成一个密码的签名，知乎后台只保存这个签名值。由于hash算法是不可逆的，那么黑客即便得到这个签名，也丝毫没有用处；而如果你在网站登陆界面上输入你的密码，那么知乎后台就会重新计算一下这个hash值，与网站中储存的原hash值进行比对，如果相同，证明你拥有这个账户的密码，那么就会允许你登陆。

#### MD5/SHA

MessageDigest是一个数据的数字指纹，即对一个任意的长度的数据进行计算，产生一个唯一指纹号。

messageDigest特性：

* 两个不同的数据，难以产生相同的指纹号
* 对于特定的指纹号，难以逆向计算出原始数据

代表算法：MD5/SHA

#### HMAC

为了防止黑客通过彩虹表根据哈希值反推原始口令，在计算哈希的时候，不能仅针对原始输入计算，需要增加一个salt来使得相同的输入也能得到不同的哈希，这样，大大增加了黑客破解的难度。salt就是key。

#### 非对称算法

你只要想：既然是加密，那肯定是不希望别人知道我的消息，所以只有我才能解密，所以可得出公钥负责加密，私钥负责解密；同理，既然是签名，那肯定是不希望有人冒充我发消息，只有我才能发布这个签名，所以可得出私钥负责签名，公钥负责验证。

公钥用于加密，私钥用于数字签名。数字证书用来确保公钥的合法性。

* RAS（以作者首字母命名）

* DSA（Digital Signature Algorithm）

  DSA一般用于数字签名和认证，发送者使用自己的私钥对文件和消息进行签名，接收者收到消息后使用发送者的公钥来验证签名的真实性。DSA不能用于加密和解密，也不能进行密钥交换，只用于签名，它比RSA要快的多。

* ECC（Elliptic Curves Cryptography）椭圆曲线算法

  优点：和RSA相比，相同密钥长度下，安全性更高，计算量更小

* ECDSA（椭圆曲线数字签名算法）

  使用椭圆曲线密码（ECC）对数字签名算法（DSA）的模拟。

  ​

  ​

  ​

  ​

  ​

  ​

  ​