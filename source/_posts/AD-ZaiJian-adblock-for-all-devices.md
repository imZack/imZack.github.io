title: 'AD-ZaiJian: 廣告再見'
date: 2015-09-05 16:45:23
tags:
  - Docker
  - DNS
  - AdBlock
  - Synology
  - Raspberry Pi
  - Projects
---

## 起因
![](https://bd23.https.cdn.softlayer.net/80BD23/142.4.51.106/blog/wp-content/uploads/2015/08/ad-blockers-test.png)

幾天前看到幾篇瀏覽器 [AdBlock 以及其他外掛效能大戰](https://www.raymond.cc/blog/10-ad-blocking-extensions-tested-for-best-performance/view-all/)，無意間看到一個有趣的專案：[Pi-Hole (RASPBERRY PI AD-BLOCKER)](http://Pi-Hole.net/)。

原來是透過 [**dnsmasq**](https://en.wikipedia.org/wiki/Dnsmasq)  來提供 dns service (tcp/udp 53)，並藉由網路上定期維護的幾份黑名單來阻擋域名解析...

> Dnsmasq is a Domain Name System (DNS) forwarder and Dynamic Host Configuration Protocol (DHCP) [(more information)](https://en.wikipedia.org/wiki/Dnsmasq)

這樣就可以把廣告 **擋在你的電腦之前**；而不是先讀取完廣告，才將其隱藏或刪除網頁上的元素！

<!-- more -->
## DEMO

<iframe src="https://player.vimeo.com/video/138398654?autoplay=1&loop=1&color=ffffff&title=0&byline=0&portrait=0" width="700" height="337" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

## 原理

**原本顯示廣告流程應為：**

1. 從 *好棒棒廣告公司.com* 取得廣告程式 or 影片 or 圖片
2. 詢問 DNS Server adserver.com IP 位置  **<--- 黑手伸入的地方**
3. Download IP 位置的廣告
4. 顯示/執行 廣告

**透過修改本機裝置的 DNS Server 將其指為 127.0.0.1 位置，廣告流程就會變成：**

1. 從 *好棒棒廣告公司.com* 取得廣告程式 or 影片 or 圖片
2. 詢問 AD-ZaiJian (DNS Server) *好棒棒廣告公司.com* IP 位置
3. AD-ZaiJian 中的 dnsmasq 開始查詢 *好棒棒廣告公司.com* 是否存在黑名單中
**是** 就回覆 127.0.0.1，**否** 將其查詢往上透通 (預設 8.8.4.4, 8.8.8.8 Google 大神 DNS Service)
4. 如此一來 *好棒棒廣告公司.com* 的廣告即無法被成功下載 (因為 127.0.0.1 是...你懂得)

> 原始版本的 Pi-Hole 比較搞剛，還多用了一個 [Web Server 上面放空白網頁跟圖片](http://jacobsalmela.com/block-millions-ads-network-wide-with-a-raspberry-pi-hole-2-0/#htmloptions)，我目前覺得有點多餘跟複雜

## 修改 Pi-Hole 成為 Docker Image

## 安裝

由於已經包裝成 Docker Image 並放置在 Docker Hub 上公開下載；所以我們可以很輕鬆地在不同平台上運行 AD-ZaiJian

[![](http://dockeri.co/image/zack/ad-zaijian)](https://hub.docker.com/r/zack/ad-zaijian/)

[安裝 Docker 請參考官方網站](http://docs.docker.com/linux/started/)

### Raspberry Pi 2 與 其他支援 Docker 的平台 (Ubuntu/Debian/...)

```
λ ~/ export IMAGE_NAME=zack/ad-zaijian-armhf        # (Raspberry Pi 2 為 zack/ad-zaijian-armhf)
                                                    # (其他 amd64 架構為 zack/ad-zaijian)
λ ~/ docker pull $IMAGE_NAME                        # 把 Image 抓回
λ ~/ docker run -d --name ad-zaijian \
    -p 53:53/tcp -p 53:53/udp \                     # 透通 tcp/udp 53 port
    -v `pwd`:/data \
    --cap-add=NET_ADMIN \
    $IMAGE_NAME
```

搞定，接下來把手機/裝置的 DNS 設定改為 Server IP，這邊例子為 192.168.1.130，大功告成！

### Synology DS 713+

圖片比較多的關係，另開一篇...[說明：安裝 AD-JaiJian 在 Synology DS 713+(尚未完工)](#)

## 原始碼

<div class="github-card" data-github="imZack/ad-zaijian" data-width="400" data-height="300" data-theme="medium"></div>
<script src="//cdn.jsdelivr.net/github-cards/latest/widget.js"></script>
