# Netfilter Framework

## Background
- Linux 2.3.x 時期 Paul Rusty Russel 寫的
- 基本上就是插入一些 Hooks 到 kernel module
- Iptables 常常跟 Netfilter Framework 搞混，Iptables 屬於 Netfilter Framework 的一員

## Hooks and The Callback Functions

有五種 Hooks 會插入在 Linux networking stack 中的不同階段

```
--> PREROUTING ---> [ROUTE] ---> FORWARD -------------------> POSTROUTING -->
                      |                              |
                      |                           [ROUTE]
                      |                              |
                   LOCAL IN ---> LOCAL PROCESS --> LOCAL OUT
```

1. PREROUTING: 所有封包都會經過這個階段，Port
Address Translation (NAPT), Redirections, Destination Network Translation (DNAT) 皆是在這裡被實現
2. LOCAL INPUT: 只要是封包傳送到本機，最後一站會到達這裡
3. FORWARD: 只要封包不是要送到本機(如：要經過防火牆)，會抵達這個階段
4. LOCAL OUTPUT: 從本機發出的封包第一站會經此
5. POSTROUTING: 當路由被決定後，離開本機端的封包會經過此。Source Network Address Translation (SNAT) 在此被實作
Source Network Address Translation (SNAT) 在此被實作

### 可以依照封包目的地分成三種類型

1. 穿過防火牆: PREROUTING > FORWARD > POSTROUTING
2. 進入防火牆(本機): PREROUTING > LOCAL IN
3. 離開防火牆: OUT > POSTROUTING

### Callback 可以分為幾種

1. ACCEPT: 讓封包通過此 Stack 繼續前進
2. DROP: 直接把封包拋棄
3. QUEUE: 丟入 userspace nf_queue，讓使用者程式來處理封包
4. STOLEN: 暫時保管封包，直到發生特定事件。通常用在 collect defragmented IP packets
5. REPEAT: 強迫封包再次進入 Hook

關於封包在 Linux Kernel 是怎麼旅遊的可以參考：

## The Connection Tracking System and the Stateful Inspection

以往在做 Packet Filtering 的時候都是透過封包的標頭檔，像是 IP, PORTS, Destination 在這幾年，這種方式已經透露出它的缺點：DDoS 攻擊容易...

Connection tracking system 會儲存連線資訊在記憶體中，包含的資訊有 source and destination
IP addresses, port number pairs, protocol types, state, and timeout 透過這些資訊，可以用比較智慧的方式來來過濾封包。

這邊要特別記住一點是，Connection tracking system 從不過濾封包，而只是記錄封包路過 Network Stack 的行蹤；還有一些例外狀況，如：記憶體滿了封包被扔掉。

> Connection tracking system just tracks packets; it does
not filter.

### 關於 State

1. NEW: 新連線剛開始，表示抵達的封包是通過驗證的 (如：TCP 的 SYNC 封包)；防火牆只看到單方的傳輸(如：防火牆尚未看到任何回覆的封包)
2. ESTABLISHED: 連線已經建立，到達這個狀態表示防火牆已看到兩邊雙向溝通
3. RELATED: 這是一個被期待發生的連線，詳見 "Helpers and Expectations"
4. INVALID: 隸屬於特別狀態，管理者可設定去紀錄或拋棄此類封包

### 綜觀
