---
layout: post
title: v2ray 结合 iptables 做透明代理
---

有些软件或者游戏没法设置代理，又需要走 v2ray 的时候，用 `dokodemo-door` 这个 protocol 配合 iptables 可以实现局域网其他机器无感知透明代理。

很简单，先修改 `config.json`，多加一个 dokodemo-door 协议的 inboundDetour，然后给代理的 outbound 加个 tag 比如 `proxy`

```json
"inboundDetour": [
    {
    	"protocol": "dokodemo-door",
    	"port": 8081,
    	"domainOverride": ["tls","http"],
    	"settings": {
    		"network": "tcp,udp",
    		"followRedirect": true
    	},
    	"tag":"forwardingport"
    }
]
```

在 `routing.settings.rules` 里多加一个策略

```json
"routing": {
    {
        "type": "field",
        "inboundTag": ["forwardingport"],
        "outboundTag": "proxy"
    }
}
```

重启 v2ray，在命令行下 root 身份执行

```sh
iptables -t nat -N V2RAY
iptables -t nat -A V2RAY -d 192.168.2.0/16 -j RETURN # 局域网网段
iptables -t nat -A PREROUTING -p tcp -j V2RAY # tcp 全部走 V2RAY 这个链
iptables -t nat -A PREROUTING -p udp -j V2RAY # udp 全部走这个链
```

这样准备工作就做好了，这只是流量都走到 V2RAY 这个 nat chain，现在这个 chain 还没有任何规则，跟没加是一样的。

然后把需要代理的流量转发到本地 dokodemo-door 端口，比如目标全部 tcp 21 端口的流量

`iptables -t nat -A V2RAY -p tcp --dport 21 -j REDIRECT --to-ports 8081`

比如全部来自 192.168.2.100 的流量

`iptables -t nat -A V2RAY -p tcp -s 192.168.2.100 -j REDIRECT --to-ports 8081`

全部流量都透明代理

`iptables -t nat -A V2RAY -j REDIRECT --to-ports 8081`

删除某条规则的话直接把 `-A` 换成 `-D`，或者 `iptables -t nat -F V2RAY` 全清空。