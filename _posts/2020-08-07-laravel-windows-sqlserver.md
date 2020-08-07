---
layout: post
title: 在 windows server 2008 安装laravel app+sqlserver
---

距离上一次用win server有15年了，这次图省事直接用的宝塔面板。

浪费我时间的地方：

1. windows server 的 iis 或者 http 服务要关掉，不然总有 pid 4 占用80端口。（server 2008 需要改注册表来关，其他手段都不好用）。
2. 宝塔装完 php 后，设置里有禁用函数，都删掉，不然laravel用的组件有各种莫名其妙的表现。
3. nginx 的配置文件 fastcgi_param 部分可以用 linux 版本的配置，不用他原来的。
4. 用 sql server，pdo_sqlsrv 驱动和 odbc 驱动要装。去微软网站直接找。
5. 为了用 deploy 部署，装了 git 和 sshd。sshd 可用微软自己搞的 `win32-openssh`。（新版winserver可用自带的？）。为了用 publickey 认证，sshd_config要注释掉最后带`__PROGRAMDATA__`那两行，`./ssh/authorized_keys` 要用记事本编辑，最后加个回车（`CRLF`）。ssh的默认shell要改为bash之类，通过注册表改（大概是在 `HKLM\SOFTWARE\SSH` 里新建一个 key/value pair）。
6. Windows 的目录分隔符是 `\`，习惯地写成`/`结果出了好多错。
7. php artisan schedule:run 写到批处理里，用set设置环境变量，用宝塔面板的定时任务执行（因为用win的计划任务不知道为啥执行不成功，也没去研究）。

我就是个windows盲，更何况这还是个 2008，搞了一下午加一晚上。

这种事情基本不会再做，各种命令和注册表细节都没记，需要时再google吧！