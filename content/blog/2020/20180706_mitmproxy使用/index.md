---
title: 网络抓包

date: 2020-07-06

tags: [网络]

---

要正确地抓包其实并不容易，如果只是HTTP接口的话，问题好像要简单很多，但涉及到HTTPS的话，数据的编解码就很费解。抓包的工具有很多选择，周围的很多人都在使用 Charles，付费软件的体验一般都比较好，这可能也是大家选择付费的原因。

除了付费的软件外，还有很多免费的工具，比如文章介绍的mitmproxy，以及Wireshark、tcpdump等。我每个工具都使用过，但每个工具都用不好，这本身就很糟糕，但自己不自知。一句废话送给读到这篇博客的各位：

> 不广求，故得；不杂学，故名

## https 编解码

HTTPS交互引入了数据加密，对称加密、非对称加密、数字证书等内容，理解它的基本原理可以帮助我们理解网络抓包。

什么是对称加密？我的理解是“一把锁两把钥匙”，你和我做安全交易，我们都可以把东西锁起来，通过公开的途径邮寄给对方，对方收到后，用钥匙直接打开。但你和我在网络上是两个不熟悉人，我买了锁之后怎么把钥匙给你就很困难。

什么是非对称加密？我的理解是“一把锁一把钥匙”，你和我做安全交易，你拿锁，我拿钥匙，你把东西锁起来然后邮寄给我，我用钥匙直接打开。但是，如果我要给你邮寄东西怎么办呢？还需要另外一把锁和钥匙，这次换我那锁，你拿钥匙。

## mitmproxy

介绍一款非常好用的抓包工具，官网地址：[https://www.mitmproxy.org](https://www.mitmproxy.org)。实际上，在调试苹果`IAP`支付时，始终没有抓成功过，反而因为设置了代理，导致苹果`沙盒用户`无法成功支付。它名字的全拼是`Man-in-the-middle-proxy`，代表中间人攻击。


## 常用的快捷键

1. 在列表界面，按`回车`进入详情界面
2. 在详情界面，按`q`返回列表界面
3. 在详情界面，按`tab`键在`Request`,`Response`,`Detail`三个`tab`之间切换。按`j`，`k`可以滚动查看详情.
4. 在列表界面，按`G`跳到最新一个请求
5. 在列表界面，按`g`跳到第一个请求
6. 在列表界面，按`d`删除当前选中的请求，按`D`恢复刚才删除的请求
7. 在列表界面，按`z`清空请求列表


## 常用的过滤表达式

列表界面,按`f`进入过滤模式。详细的过滤表达式，可以查看：[`Filter expressions`](https://docs.mitmproxy.org/stable/concepts-filters/)。



1. `~h regex	Header`
2. `~u regex	URL`
3. `~m regex    Method`

## 原理

 1. [Subject Alternative Name](https://en.wikipedia.org/wiki/Subject_Alternative_Name)：is an extension to X.509 that allows various values to be associated with a security certificate using a subjectAltName field. These values are called Subject Alternative Names (SANs). Names include
 2. [Server Name Indication](https://en.wikipedia.org/wiki/Server_Name_Indication)： is an extension to the TLS computer networking protocol by which a client indicates which hostname it is attempting to connect to at the start of the handshaking process. This allows a server to present multiple certificates on the same IP address and TCP port number and hence allows multiple secure (HTTPS) websites (or any other service over TLS) to be served by the same IP address without requiring all those sites to use the same certificate. 

