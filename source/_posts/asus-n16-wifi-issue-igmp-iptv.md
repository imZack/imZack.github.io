title: '華碩 N16 無線網路連不上 (IGMP、中華MOD)'
date: 2014-07-23 11:45:58
tags:
	- ap
	- network
	- mod
---

今天早上因為颱風的關係跳電兩次，家裡面的 [RT-N16](http://www.asus.com/tw/Networking/RTN16/) 忽然失億了...於是我就被我媽叫起床修理，因為他們起床發現沒有網路快要抓狂了。囧rz...

把 AP 重開後確認他的確是失億了，原本的設定都消失了。大概是 Nand flash 不正常斷電損毀後他自動復原吧？(瞎猜)，總之我就把 Hinet 小卡片帳號密碼打上，常用設定設好後...發現 WIFI 怎麼看的到吃不到，一直連不上！

由於我家網路架構比較特殊，為了要有 `Gbps LAN` 以及 MOD 正常使用並且不干擾其他裝置

線路圖如下：

```

牆壁網路孔 <---> SWITCH <---> MOD, 小米盒子, Apple TV (都是 DHCP)
    |
    |
    |
ASUS N16   <--WAN and LAN 各接一條--> 中華電信小烏龜
    |
    |
  PC (DHCP), NAS (PPPoE 自己撥號取得固定 IP)

```

1. 其中小烏龜停用 `DHCP` 功能，統一由 N16 配發。
2. 小烏龜設定 `PORT ISOLATION` 功能，將與 N16 LAN/WAN 兩孔隔開
3. N16 內部網路設定(LAN) - IPTV 中的 `啟動群播路由`、`Enable efficient multicast fowarding` **一定要一定要一定要打開**，今天我就是忘記打開所以 WIFI 一直連不上。這個要是不開 MOD 的廣播封包會把你的網路炸爛...
>想不到遇到有人接法跟我一樣，然後遇到一樣[問題](http://www.mobile01.com/topicdetail.php?f=110&t=2387032)



筆記一下，免得下次 N16 罷工我又忘了怎麼設定...

