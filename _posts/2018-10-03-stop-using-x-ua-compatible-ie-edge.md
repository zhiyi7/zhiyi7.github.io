---
layout: post
title: X-UA-Compatible IE=Edge 可以丢弃了
---

绝大部分网页都可以看到 `<meta http-equiv="X-UA-Compatible" content="IE=edge" />` 这个 meta tag，想避免用旧模式渲染产生问题。但根据微软官方的解释，这样写在大多数情况下不仅没什么效果，还会拖累页面表现，连微软自己都不建议这样做。

StackOverflow 有一个问题[详细解释了这点](https://stackoverflow.com/questions/26346917/why-use-x-ua-compatible-ie-edge-anymore)。

简单来说为什么不再需要在页面头部写 `<meta http-equiv="X-UA-Compatible" content="IE=edge" />`：

1. 没有这个 meta tag 的话，IE 会根据 !DOCTYPE 声明来选择渲染引擎，只要有 `<!doctype html>` 就会用最新引擎渲染，除非：
    1. 用户手动把网站加入了兼容列表
    2. 某些老旧国产浏览器自动用老旧兼容模式
2. 只有 IE8/9/10 支持这 meta tag，这三个版本现在已经不再维护，已经问世两年多的 IE11 开始不再管这个 meta。
3. 有可能会造成页面重新渲染，尤其是在国产浏览器上。
4. 造成页面 doctype validation 有 error 产生。

解决方法也很简单，在页面上去掉这行代码，后端判断如果 UA 是 MSIE，多返回一个 `X-UA-Compatible: IE=edge,chrome=1` 的 header 就可以。

对于 360 这种双核浏览器，可以加一个 `<meta name="renderer" content="webkit" />` 来让它默认用 chrome 内核渲染（虽然它包含的总是低版本 chrome）。

加上还是去掉这个祖传的 meta tag 影响不是很大，但知道所以然总归没有坏处。