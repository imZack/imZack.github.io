title: 移除/停用 Ionic Splash Screen
date: 2015-09-09 23:21:11
tags:
  - Ionic
  - ios
  - app
---

這幾天再開發 [TNUA BUS APP](http://yulun.me/tnua/) iOS 版的時候，發現預設有惱人的 Splash Screen...

什麼是 Splash Screen ？就是一開啟 APP 他會跳出來顯示 LOGO 之類的那個玩意，然後過幾秒(或是等待背景程式預備狀態)後自動消失。



因為我把 TNUA BUS APP 定調為一個簡單速查的工具，這個過十分沒有必要。Google 了一下發現答案還滿多種的 = =+

於是找到了一個官方有提到的設定參數

[Customizing the Splash Screen](http://learn.ionicframework.com/formulas/splash-screen/)

直接在 Ionic 專案根目錄中的 `config.xml` 加入下方程式碼：
```
<preference name="AutoHideSplashScreen" value="true" />
```

打完收工。

**UPDATE 2015/09/13**: 原來上面的方式無法關閉（上次不知道為什麼鬼打牆ㄡ...）
要進 Xcode Project Setting 裡面修改把 **Launch Screen File** 清空，如下圖：

![xcode remove splash screen](https://farm1.staticflickr.com/675/21189569449_067e3168be_o_d.png)
