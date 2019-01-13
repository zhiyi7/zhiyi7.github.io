---
layout: post
title: 提升 phpunit 测试速度的小技巧
---

[Laravel News](https://laravel-news.com) 有一篇文章介绍了几个关于提升 phpunit 测试速度的小技巧，主要是以下几个方面：

- 并行测试（用 paratest 包）
- 只运行失败的测试（依赖 phpunit 7.3 新增的特性）
- 把运行缓慢的测试编成一组，刨除他们或者单独跑（利用 @group 标注）
- 只跑指定的测试（用 --filter 选项）
- 减少 password hash rounds（仅针对 bcrypt）
- 使用内存数据库（sqlite 的内存库）
- 禁用 xdebug
- 终极大招：改慢的测试代码让他更快……

详细内容移步原文：[Tips to Speed up Your Phpunit Tests](https://laravel-news.com/tips-to-speed-up-phpunit-tests)