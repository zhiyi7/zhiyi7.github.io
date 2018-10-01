---
layout: post
title: 用 v2ray/ss 结合 PAC 文件科学上网
---

作为一个码农，不能上 Google 等于自废武功。很多年前，我的科学上网方式是路由器刷 DD-WRT 拨 VPN，用 iptables 让国内 IP 走 WAN 路由，国外 IP 走 VPN 出口来实现智能分流。后来行政手段配合 GFW 深度探测学习拦截 VPN 流量技术，VPN 被严格限制，蓝灯/ss/v2ray 等变成了主流科学上网形式。

v2ray 自带分流功能，在配置文件里写好，访问国内地址时就可以不走上级服务器。然而这个自动分流功能，全部流量实际还是要 v2ray 程序本身做处理，这样就成了瓶颈。在我的 Asus AC88U 上，不通过 v2ray 可以跑满 250M 带宽，通过 v2ray 只能有200M左右，同时连接数多的时候可以明显感知到这个性能损失。

另一个问题是对于 Web 开发者，如果全局设置为使用代理，那就要额外小心的配置 v2ray，否则会出现访问开发服务器域名时被 v2ray 解析而访问失败。

幸好有 PAC（Proxy auto-config）技术。PAC 文件是一个 javascript 脚本文件，浏览器访问一个网址时会执行里面的 `FindProxyForURL(url, host)` 函数，这个函数返回一个字符串，浏览器根据返回的字符串来确认是用代理还是直接访问。iOS / macOS / Windows 都支持这个技术。那么直接用 PAC，让浏览器访问网站时判断该不该走代理就可以实现分流了。

我写了一个自动脚本 [gfw-pac](https://github.com/zhiyi7/gfw-pac)，通过 gfwlist 和中国 IP 地址生成 PAC 文件。生成的 PAC 规则会先判断域名是否在白名单或者在 gfwlist（一个网友自己维护的被墙网址列表）。对存在于 gfwlist 的域名直接使用代理，对存在于白名单的域名直接让浏览器不用代理，其他网址就解析出 IP 后判断是否需要走代理，这样就省去了先解析域名再判断 IP 段的时间损失。

### 使用方法

1. 首先要有一个能用的代理服务器，然后 `git clone https://github.com/zhiyi7/gfw-pac.git` 或者直接下载 <https://github.com/zhiyi7/gfw-pac/archive/master.zip>。 
2. 用文本编辑器打开 `gfw.pac`，修改第一行的 `var proxy = "PROXY 192.168.2.1:10080; DIRECT";` 换成自己的代理服务器。如果是 SOCKS 协议，则写法为 `SOCKS5 IP:PORT`，例如 `var proxy = "SOCKS5 192.168.1.1:1080; DIRECT"`。分号后的是 fallback，即如果前面的失效，浏览器会尝试使用后边的。`DIRECT` 代表不使用代理。
3. 把 `gfw.pac` 放到 web 服务器或者本地，然后在各个系统网络设置的自动代理配置文件里填上这个文件的 URL。如果是本地文件系统，则用 `file://` 开头（并不是所有系统或者浏览器都支持这种，为了兼容性尽量放在 web 服务器上，也便于其他设备使用）

### 自定义域名

1. 自定义不需要用代理的域名：找到 `gfw.pac` 里 `var directDomains =`，把不需要走代理的域名按照格式填进数组。
2. 自定义需要用代理的域名：找到 `gfw.pac` 里 `var domainsUsingProxy =`，把域名按照格式填进数组。
3. 自定义开发用顶级域。一些开发者会把例如 `*.test` 作为开发域名，这样可以配置一个 `.test` 的 TLD 就让所有这个顶级域下的域名都不走代理。找到 `gfw.pac` 里 `var localTlds = `，把顶级域填进去。这里不支持 `.com.cn` 这种实际为二级域的顶级域。记得域名前面的 `.` 要填写，例如 `.mycompany`。

### 自己生成

我有空时会把基于最新国内 IP 和 gfwlist 的 pac 文件 commit 上去，但并不保证及时更新。如果本机有 python 环境，可直接使用 `gfw-pac.py` 自己更新。关于这个脚本的使用说明，直接参考 [项目主页](https://github.com/zhiyi7/gfw-pac) 的说明即可。

*Across the great wall we can reach every corner in the world.*