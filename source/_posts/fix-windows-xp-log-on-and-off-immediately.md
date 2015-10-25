title: 'Windows XP 登入後立刻被登出 (忽然要輸入登入密碼)'
date: 2014-06-24 00:19:47
tags:
	- Windows
---

這一天老媽忽然把電腦搬到我面前告訴我，**不能開機了** ...

看了一下，奇怪原本不需要輸入密碼的電腦怎麼忽然要輸入使用者名稱與密碼？心想應該是他們不小心弄到什麼了！於是就開始輸入一些例如：`Administrator`試圖登入。

顯然事情沒有這麼簡單，所以我又找了一些關於**忘記密碼**的解法。當然這過程當中可以說是困難重重，因為我的電腦都是 **sata** 介面，連 **光碟機**、**IDE** 什麼的都沒有，也不能用 **USB** 來製作救援。因為電腦已經十歲了，還無法支援 **USB** 開機。原本想說拿一台外接光碟機來用，但是發現我的快接線壞掉了...真是一波三折。

總結一下，最後就是拿了 **伽利略 旗艦版 SATA&IDE TO USB3.0 光速線** 這個好物來接到我電腦上，並搭配 **VirtualBox** 來使用。

1. ERD Commander 2005
2. 快接線

就這樣，把使用者密碼給改了。原本以為故事到此結束！但是我發現根本無法登入！就算密碼對了，**登入後也會立刻被登出**

於是找到了這篇 [Windows Log on and Log off immediately.](http://support.microsoft.com/kb/555648/en-us)

關鍵在於這個 `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon` 新增這兩個鍵

	Shell = explorer.exe
	Userinit = X:\windows\system32\userinit.exe

ps. `HKLM = HKEY_LOCAL_MACHINE`

以上，終於達成修理電腦的任務。


*想不到十年的電腦到現在還是一尾活龍阿...*

