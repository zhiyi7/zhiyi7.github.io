---
layout: post
title: 308 Permanent Redirect
---

我们对 HTTP 协议状态码的 301 和 302 已经用的很多了，301 代表资源永久移动，客户端以后应该去访问新资源 URL。对于各级缓存来说这个代码也会造成访问旧资源时缓存浏览器直接向客户端返回 `HTTP/1.1 301 Moved Permanently`，除非有新的 Cache Control 规则。302 是告诉客户端地址临时变化，一般于当次访问做 Redirect，下次请求时还会访问原始 URL。

对于 GET 请求这是没问题的，但历史上很多浏览器对于 POST 请求后收到的 301 和 302 的实现是，在收到 301/302 后，把 POST 请求转换成了 GET 请求。客户端 POST 请求一个 URL，服务器返回 301 或 302，浏览器没有按照预期的去向新 Location 发送同样的 POST 请求，而是向新地址发送了一个 GET 请求。这在某些情况下就造成了请求失败。

RFC 对于这个问题，对 HTTP Status Code 做了更新，一个是 `307 Temporary Redirect`，一个是 `308 Moved Permanently`。

`307 Temporary Redirect` 在 [RFC 7231](https://tools.ietf.org/html/rfc7231#section-6.4.7) 就有提及，`308 Moved Permanently` 是 2014 年在 RFC 7238 作为实验性标准提及，2015 年 4 月 [RFC 7538](https://tools.ietf.org/html/rfc7538) 做了确认。

308/307 和 301/302 非常相似，唯一的区别是，308/307 明确规定不允许将 Request Method 从 POST 改为 GET。

详情见 [RFC 7538](https://tools.ietf.org/rfc/rfc7538.txt)。

**然而...**

**这个世界上还有一个阴魂不散的浏览器叫 IE，至少在 IE11 上，对于 308 还没有做出正确的处理。**