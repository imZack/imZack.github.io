title: VMware 在 HOST OS 休眠後網路斷線問題
date: 2014-08-31 23:43:57
tags:
	- VMware
---

這幾天開始用 VMware 用 Ubuntu 當工作環境，忽然遇到一個問題就是只要我的 HOST OS 從睡眠中起床後，GUEST OS 就會沒有網路可用！

搜尋了一下...找到了這篇滿久以前的文章 (是說既然都這麼久了 沒修好是有原因的?)

<!-- more -->

[原文網址](https://communities.vmware.com/thread/175585?start=0&tstart=0)

> Running VMware Workstation 6.5 and Vista 32-bit with a wired NIC.  My Bridged networking stops working after Vista sleeps or hibernates. A restart is required to restore its operation. NAT networking works fine. 
Anyone have any ideas how to restore it without a reboot?

結果後面有人回應可使用 cmd (請用管理員權限開啟) 輸入以下指令
```sh
net stop vmnetbridge
net start vmnetbridge
```


此法的確可行，不過每次都這樣未免太蠢了...

原文作者在 4 年後回應了自己

> Well several years after I encountered this problem it appears I have a fix. I was using VMWare's Virtual Network Editor (included with VMWare Workstation) and I noticed bridged mode was set to Automatic. I changed it to bind to my specific network adapter because I thought that might help. It does. I no longer have to issue "net stop vmnetbridge && net start vmnetbridge" on hibernate or sleep resume. This is in Workstation 9 on Windows 7 x64.
 
> If anyone else encounters this problem you may want to try doing that. The disadvantage is if you use multiple network adapters is I guess it will not switch over automatically? In Workstation 9 that doesn't seem to work anyway (the whole reason I was using the network editor in the first place). What I've ended up doing is I have two virtual network adapters, one bridged to each physical adapter and I let the VM access them both. I imagine that could be pretty inconvenient if you have a lot of VMs though.

OK，簡單說就是把 VM 中的 **Virtual Network Editor** 裡面的 **bridge mode** 從原本的 **automatic 改為固定你要用的那張網卡**，問題就解決了！總覺得這個解法只算岔強人意？不知道有沒有更好的解法，不過沒時間了先這樣吧！

