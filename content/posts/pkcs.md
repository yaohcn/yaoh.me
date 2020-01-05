---
title: "pkcs"
date: 2020-01-05T19:47:52+08:00
draft: false
---
关于证书和密钥
------
### 对称加密和非对称加密
对称加密算法的优点是加解密的速度比较快，缺点是密钥容易泄露。非对称加密解决了密钥易泄露的问题，但是加解密速度比较慢。所以web开发中常采用非对称加密算法交换密钥，然后采用对称加密算法对传输的内容进行加解密。那么问题来了，什么是证书？为什么要有证书？这个问题一直不理解，直到读了这两边文章，恍然大悟。
- [数字签名是什么？](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)
- [趣聊密码学入门科普](https://yq.aliyun.com/articles/630633)

总结就是：当A和B要通信时，需要把A的公钥给B，但是B怎么确认收到的公钥就是A的，而不是别人冒充的呢？这时就需要一个权威的第三方机构来证明。证书就是第三方机构颁发的，里面包含了公钥。公钥用于加密，私钥用于数字签名。数字证书用来确保公钥的合法性。

### 数字签名的格式
工作中需要采用ecdsa算法进行验签，发现签名有好几种格式，包括DER，PEM等。要理解什么是DER，要先理解[ASN.1](https://zh.wikipedia.org/wiki/ASN.1)。
#### ASN.1
  ASN.1是一种描述数据结构的标准，ASN.1本身只定义了表示信息的抽象句法，但是没有限定其编码的方法。各种ASN.1编码规则提供了由ASN.1描述其抽象句法的数据的值的传送语法（具体表达）。标准的ASN.1编码规则有ber，cer，der等。简单的说，asn.1是标准，der是asn.1的一种实现。DER是二进制，不便于显示和传输，于是有了PEM。PEM仅仅是对DER进行[base64](http://104.199.216.176/post/view/6)编码。
#### DER
DER具体的格式没有找到中文的定义。英文定义如下：

 * A correct DER-encoded signature has the following form:
 * 0x30: a header byte indicating a compound structure.
 * A 1-byte length descriptor for all what follows.
 * 0x02: a header byte indicating an integer.
 * A 1-byte length descriptor for the R value
 * The R coordinate, as a big-endian integer.
 * 0x02: a header byte indicating an integer.
 * A 1-byte length descriptor for the S value.
 * The S coordinate, as a big-endian integer.
 * Where initial 0x00 bytes for R and S are not allowed,
 * except when their first byte would otherwise be above 0x7F
 * (in which case a single 0x00 in front is required).
 * Also note that inside transaction signatures,
 * an extra hashtype byte follows the actual signature data.
  
真实的DER格式签名数据：
> 3044022063B2600A7327976B326C1E7DC32E675BC8FA8B551117A59D75C371E6A7ACD7440220521D28F6164D7A080F4B4231088C53B119DFACF25651A019C375C051B40098EE

验签时需要把DER格式的签名转化位r|s，openssl中的转化api有点复杂，于是自己实现了一个：

```
static int d2i_sig(uECC_Curve c, const uint8_t* sig, uint8_t sig_len, uint8_t* rs) {
    int len = 0;
    int key_len = uECC_curve_public_key_size(c);
    TRACE("key_len:%d\n", key_len);

    trace_hex("sig", sig, sig_len);

    if(sig[len] != 0x30)
        return D2I_SIG_ERROR;
    len += 1;

    if((sig[len] + 2) != sig_len)
        return D2I_SIG_ERROR;
    len += 1;

    if(sig[len] != 0x02)
        return D2I_SIG_ERROR;
    len += 1;

    if(sig[len+1] != 0x00 && sig[len+1] <= 0x7F) {
        //长度小于key_len时前补0
        memcpy(rs+(key_len-sig[len]), sig+len+1, sig[len]);
        len += sig[len] + 1;
    }
    else if(sig[len+1] == 0x00 && sig[len+2] > 0x7F) {
        memcpy(rs+(key_len-(sig[len]-1)), sig+len+1+1, sig[len]-1);
        len += sig[len] + 1;
    }
    else {
        return D2I_SIG_ERROR;
    }

    if(sig[len] != 0x02)
        return D2I_SIG_ERROR;
    len += 1;

    if(sig[len+1] != 0x00 && sig[len+1] <= 0x7F) {
        memcpy(rs+key_len+(key_len-sig[len]), sig+len+1, sig[len]);
        len += sig[len]+1;
    }
    else if(sig[len+1] == 0x00 && sig[len+2] > 0x7F) {
        memcpy(rs+key_len+(key_len-(sig[len]-1)), sig+len+1+1, sig[len]-1);
        len += sig[len] + 1;
    }
    else {
        return D2I_SIG_ERROR;
    }
    trace_hex("rs", rs, 64);
    return SDKSUCCESS;
}
```
openssl提供了命令行工具进行转换
>  * OpenSSL 工具使用
   * OpenSSL> genrsa -out app_private_key.pem   2048  #生成私钥
   * OpenSSL> pkcs8 -topk8 -inform PEM -in app_private_key.pem -outform PEM -nocrypt -out app_private_key_pkcs8.pem #Java开发者需要将私钥转换成PKCS8格式
   * OpenSSL> rsa -in app_private_key.pem -pubout -out app_public_key.pem #生成公钥
   * OpenSSL> exit #退出OpenSSL程序
  
其他概念：
- [公钥密码学标准](https://zh.wikipedia.org/wiki/%E5%85%AC%E9%92%A5%E5%AF%86%E7%A0%81%E5%AD%A6%E6%A0%87%E5%87%86)
- [X.509](https://zh.wikipedia.org/wiki/X.509#%E8%AF%81%E4%B9%A6%E7%BB%84%E6%88%90%E7%BB%93%E6%9E%84)
