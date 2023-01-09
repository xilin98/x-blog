---
date:  2023-01-09
title: HTTP 和 HTTPS
tags: 
  - 计算机网络
categories:
  - 计算机网络
---

## 什么是 HTTP 和 HTTPS

HTTP（HyperText Transfer Protocol）是一个用于分发超文本的协议，是在计算机与 Internet 之间传送超文本文档的标准。当你在浏览器中输入一个网址时，你实际上是发送了一个 HTTP 请求。服务器收到请求后，会将所请求的网页返回给浏览器，浏览器再将其渲染并显示给用户。

HTTPS（HyperText Transfer Protocol Secure）是在传输层使用加密的 HTTP，它通常用于在网络上传送敏感信息（如在线银行或电子商务）。 HTTPS 在传送数据时使用了安全套接层（SSL）或者传输层安全（TLS）来加密数据。

// TODO 进一步补充相关原理
// 用 nodejs 实现一个 http