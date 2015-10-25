title: 發布 IONIC 製作的 APP 至 Play Store (Android)
date: 2014-08-28 21:56:53
tags:
	- ionic
	- play store
	- tnua
---

這篇欠滿久的，之前發表的「[北藝大校車 APP](http://yulun.me/tnua/)」是使用 IONIC 完成的，一直以來都習慣用 Eclipse 開發 Android 程式。之前也是透過 Eclipse 裡面的 Deploy 功能來簽署(sign) .apk 檔案，現在要自己 key 指令紀錄一下，之後更新可以用！

<!-- more -->

對照官方教學一步一步動手吧！ [>> 原文教學在此 <<](http://ionicframework.com/docs/guide/publishing.html)

## Remove console plugin
先把 cordova 的 console plugin 移除(當然你也可以不移...)

```sh
$ cordova plugin rm org.apache.cordova.console
```

`platforms/android/AndroidManifest.xml` 裡面把 `debuggable` 改成 `false`
```xml
<application android:debuggable="true" android:hardwareAccelerated="true" android:icon="@drawable/icon" android:label="@string/app_name">
```

## Build apk
```sh
$ cordova build --release android
```

build 完之後會在 `platforms/android/bin` 這裡找到<strike>野生</strike>還沒有 sign 的 apk 檔案

## Generate Key
所有上架 Play Store 的程式都必須簽署過才能上架，所以這邊就要使用 JDK 內附的 `keytool` 來幫我們產生所需要的 key (如果你原本就有 key 了可以跳過這邊)
> 路徑可能是長這樣 `C:\Program Files\Java\jdk1.7.0_40\bin\keytool` ，你可以把這個目錄加到系統 path 環境變數，就可以直接在 command line 中執行了

下面是範例提供的產生參數

```sh
$ keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
```

> **注意！**請小心保管你的 key ，要不然之後 app 要更新沒有同一把 key 是不能更新的！

## Sign apk
有了野生的 `apk` 以及剛剛產生的 `key` 之後就可以來做一個**簽署 Sign**的動作

以下是我簽署的範例

```sh
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore J:\xxx\tnuabus.keystore H:\xxx\tnua-bus\platforms\android\ant-build\HelloCordova-release-unsigned.apk tnuabus
```

## Zipalign
最後一個步驟了，`zipalign` 來最佳化 apk

```sh
J:\android_project\adt-bundle-windows-x86_64-20140702\sdk\build-tools\19.1.0\zipalign.exe -v 4 H:\xxx\tnua-bus\platforms\android\ant-build\HelloCordova-release-unsigned.apk H:\xxx\tnua-bus\platforms\android\ant-build\tnua.apk
```

> `zipalign` 算是 Android SDK 的 Tool 所以請去安裝 SDK 的目錄底下尋找，或參考我上面的位置找找。

## Publish

大功告成啦，再來就是繳點錢給 Google (終身可用)，然後把 apk 丟上去、資料填一填！你的上架計畫就到此告一段落啦！
