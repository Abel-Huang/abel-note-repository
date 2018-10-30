# Java 基础面试题
1. interface 可以用哪些关键词修饰？
interface 不能使用 private、static、protected、final 关键字进行修饰，
可以使用public 和 default 进行修饰，因为所有的接口都是需要来继承的，需要注意的是
default 是可以用来修饰接口的。

2. 常见的加密解密算法
    * 摘要算法：MD5，SHA1；通过签名摘要生成固定长度的数据，一般通过字典法破解，如彩虹表；
    * 对称加密：AES 和 DES，加密密钥和解密密钥相同
    * 非对称加密：RSA，加密密钥和解密密钥不同，RSA基于大整数因数分解；还有圆锥曲线加密；
    * Base64：严格意义来说以一种编码。