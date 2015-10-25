title: Ubuntu 強制停用 IPv6 (Digital Ocean)
date: 2014-09-03 23:30:32
tags:
  - Ubuntu
  - DigitalOcean
---

這幾天要在 [DigitalOcean](https://www.digitalocean.com/?refcode=55fb1a14e9e7) 上面裝東西的時候，發現 `apt-get update` 之類的指令都非常慢，然後也注意到他試圖存取 IPv6。

<!-- more -->

因此就發了一個 Issue Ticket 給 DO 的客服，回答如下：

>Hello!
>Can you clarify the results you are seeing that show the connection as slow? 
>Just to be clear, IPv6 is not enabled in SFO droplets, so you shouldn't be able to make *any* IPv6 connection from SFO.
>
>Please let us know.
>
>Thanks, 
>Tyler 
>DigitalOcean Support 
>https://digitalocean.com/community

隨手找一下如何關掉 apt-get 的 IPv6，可以找到[這篇](http://unix.stackexchange.com/questions/9940/convince-apt-get-not-to-use-ipv6-method)

簡單說就是
```sh
sudo touch /etc/apt/apt.conf.d/99force-ipv4
sudo echo "Acquire::ForceIPv4 true;" > /etc/apt/apt.conf.d/99force-ipv4
```

> Config options Acquire::ForceIPv4 and Acquire::ForceIPv6 were added to version 0.9.7.9~exp1 (see bug 611891) which is available in Ubuntu Saucy (released in october 2013) and Debian Jessie (not released yet).

基本上這樣就完成了，如果版本不一樣的話 (not support)，可以嘗試下面其他答案。再不吝回報一下囉！


***題外話***，如果用 >>> [DigitalOcean](https://www.digitalocean.com/?refcode=55fb1a14e9e7)  這個連結註冊 DO 可以得到 $10 美金的 Credit 喔！(我可以得到 $25，來玩玩看吧！)

