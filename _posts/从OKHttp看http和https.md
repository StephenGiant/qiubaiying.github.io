---
layout:     post
title:      从OKHttp看http和https
subtitle:   https的使用和原理，以及原因
date:       2017-05-04
author:     BY
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - https

---


> 本文首次发布于 [Booker Blog](http://stephengiant.github.io), 作者 [@Booker](http://github.com/stephengiant) ,转载请保留原文链接.


# TCP连接

什么叫做连接？

当一个客户端和服务端确立了通信，这个时候我们就可以称之为确立了连接。TCP连接的过程主要是有三次握手的过程。TCP是处于协议层的协议，http在TCP层之上。

# HTTPS协议

Http协议下加了SSL安全层，用于保护HTTP的加密传输，便成为了Https协议。他的本质是在客户端和服务端间协商一套对称密钥，每次发送信息前将内容加密，收到之后再解密，达到内容的加密传输。

那么HTTPS的连接建立过程是怎样的？

1、客户端向服务器发起请求建立TLS连接，发送的信息包括接受的TLS版本，CipherSuite版本（本质就是告诉服务器接受的非对称加密、对称加密、Hash算法）和一个随机数。服务器同时也响应客户端发送一个服务器的随机数。

2、服务器发回证书。证书包含域名、地址、证书公钥、证书颁发机构、证书签发机构等信息

3、客户端验证证书（读取证书信息去验证是否合法等）。

4、证书验证通过后，客户端信任服务器，和服务器协商出一个密钥（此密钥通过非对称加密生成），即Master-Secret，这个密钥包括了客户端加密密钥、服务器加密秘钥、客户端HMAC Secret、服务器HMAC Secret。

5、之后的就和HTTP的啥一样的，区别就是数据是经过加密的。

HTTPS的核心就是加密数据部分使用公钥加密，私钥解密，验证部分使用私钥加密公钥解密。

# Okhttp使用HTTPS

这里只讲android里使用okhttp遇到https的情况

一般我们使用https协议会有两种情况，第一种就是最常见的服务器发送证书的情况，这种情况客户端一般不用做特殊的处理。第二种就是证书不是通过服务器发送的，是在客户端自己去解析并信任的，也叫做自签名证书。

getResponseWithInterceptorChain

自签名证书快捷使用方法：

okhttpclient.builder().certificatePinner(CertificatePinner.Builder().add(hostname,公钥).buid()即可

