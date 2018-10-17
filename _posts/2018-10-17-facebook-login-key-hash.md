---
layout: post
title: 解决发布到 Play Store 后 Facebook Login key hash 错误
---

android 应用发布到 google play store 后，facebook login 失效，看错误信息是 key hash 错误。原因是在 play store 里启用了 Google Play App Signing，上传包后 google 重新签名了。

解决方案很简单，在 play console 里找到版本管理-应用签名，复制 SHA-1 证书指纹，然后运行

`echo 33:4E:48:84:19:50:3A:1F:63:A6:0F:F6:A1:C2:31:E5:01:38:55:2E | xxd -r -p | openssl base64`

这个值应该会跟fb login时报错信息里的 key hash 一致。把他添加到 fb 应用设置里的 key hash 里就可以。

[stackoverflow 上的原始答案](https://stackoverflow.com/a/47609031)