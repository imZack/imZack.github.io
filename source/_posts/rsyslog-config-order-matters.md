title: Rsyslog 設定順序是有差別的 (開啟 UDP bind 127.0.0.1)
date: 2016-01-03 17:05:51
tags:
  - Linux
  - Rsyslog
  - Debian
---

在設定 Rsyslog 時，預設 Debian 7 沒有打開 local udp 514 port 接收的功能。所我就簡單加入：

```
$ModLoad imudp
$UDPServerRun 514
```

後來發現，預設是 bind 在 `0.0.0.0` 相當之危險～等於所有人都可以打 log 到你機器上，於是我又再加入：

```
$ModLoad imudp
$UDPServerRun 514
$UDPServerAddress 127.0.0.1
```

發現沒有用，還是 bind 在 `0.0.0.0`，死馬當活馬醫...調整一下順序：

```
$ModLoad imudp
$UDPServerAddress 127.0.0.1
$UDPServerRun 514
```

這樣就可以了（暈），原來 Rsyslog 的設定順序是不可逆的。找個時間可以再深入研究一下...（遠目）

檢查一下 `netstat -nlp | grep 514`：

```
udp        0      0 127.0.0.1:514           0.0.0.0:*                           12274/rsyslogd  
```

打完收工。
