title: 傳奇的 PL2303 USB-to-Serial 在 Windows 8 使用
date: 2015-09-17 22:32:46
tags:
  - driver
  - funny
---

這條神奇的 USB-to-Serial 線是之前買 cubie board 時送的，很方便，簡直就是嵌入式系統初心者必備。直到有一天升上了 Windows 8 ...

**就再也不能用了 WTF ** 一直出現 error code 10 這個裝置無法啟動 (代碼10)

之前還傻傻的開 VM 透通進去使用，今天不死心又上網找了一下...果然遇到很多可憐的碼農遇到跟我一樣的問題 haha

解法：

1. 插上線→先把舊 Driver 刪除→拔線
2. 上網找舊版 Driver (PL2303 3.3.2.105)
3. 管理者權限安裝下載的 Driver
4. 插上線，裝置管理員中更新驅動程式，選擇

reference: http://www.sllk.net/wordpress/?p=982
