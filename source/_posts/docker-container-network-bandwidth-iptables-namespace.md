title: 查看 Docker 每個 Container 流量 (使用 ip nets 指令)
date: 2016-01-03 23:17:12
tags:
  - Docker
  - Container
  - Linux
  - Iptables
---

忘記是哪一天，忽然想檢查一下各別 Container 的流量用了多少，上網隨手找了一下，看到可以使用 **ip-netns - process network namespace management** 這個指令來查看不同 namespace 底下的流量。

具體方式如下：

1. 取得 Container PID, 這邊我查看名為 wordpress_wordpress_1 的 container
```
λ ~/docker inspect -f '{{ .State.Pid }}' wordpress_wordpress_1
658
```

2. 建立連結至 /var/run/netns/ 供 ip netns 使用
```
λ ~/sudo ln -sf /proc/658/ns/net /var/run/netns/wordpress
```

3. 利用 ip netns 接 iptables 流量就一目了然了！
```
λ ~/sudo ip netns exec wordpress iptables -L -nv
Chain INPUT (policy ACCEPT 639 packets, 106K bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 610 packets, 882K bytes)
 pkts bytes target     prot opt in     out     source               destination
```

Reference: [Counting bandwidth from a Docker container](http://serverfault.com/questions/615854/counting-bandwidth-from-a-docker-container)
