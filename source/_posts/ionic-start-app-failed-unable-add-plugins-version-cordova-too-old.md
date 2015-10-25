title: 'ionic 之第一次安裝就失誤 Unable to add plugins'
date: 2014-07-04 00:23:34
tags:
	- Node.js
	- ionic
	- Mobile App
---

今天忽然看到有人分享 ionic 相關訊息，一時手癢想說來玩一下，沒想到就碰壁了...

先聲明以下環境是 Windows 8.1 64-bit，我按照官方網站上面的[指示](http://ionicframework.com/getting-started/)

```bash
$ npm install -g cordova ionic
```

結果不知道為什麼，我直接在開始選單執行 `Node.js command prompt`系 統會說權限不足(Try Administrator)，我自己開 `cmd` 就可以，也許這是種下之後安裝錯誤的種？

第二步要開新專案，有 **blank**, **tabs**, **sidemenu** 三種可以選，想說來個 **tags** 好了...

```bash
$ ionic start myApp tabs
```

結果...

```bash
H:\Dropbox\project\mobileapp>ionic start newapp tabs
Running start task...
Creating Ionic app in folder H:\Dropbox\project\mobileapp\newapp based on
 tabs project

DOWNLOADING: https://github.com/driftyco/ionic-app-base/archive/master.zip

DOWNLOADING: https://github.com/driftyco/ionic-starter-tabs/archive/master.zip
Initializing cordova project.
Fetching plugin "org.apache.cordova.device" via plugin registry
Fetching plugin "org.apache.cordova.console" via plugin registry
Fetching plugin "https://github.com/driftyco/ionic-plugins-keyboard" via git clo
ne
Error: Command failed: fatal: could not create work tree dir 'C:\Users\YOURUSERNAME\AppD
ata\Local\Temp\plugman\git\1404399211161'.: No such file or directory

    at ChildProcess.exithandler (child_process.js:647:15)
    at ChildProcess.EventEmitter.emit (events.js:98:17)
    at maybeClose (child_process.js:753:16)
    at Process.ChildProcess._handle.onexit (child_process.js:820:5)
ERROR: Unable to add plugins. Perhaps your version of Cordova is too old. Try up
dating (npm install -g cordova), removing this project folder, and trying again.

Exiting.
```

errr....我不是剛剛才裝了 Cordova 嗎？怎麼可能過期阿！於是上網找尋一下，發現有人情況跟我一樣，他的解法是：

以下為關鍵片段，沒有這個目錄或檔案...通常是在不存在的資料夾下產生檔案2k7
情況會出錯，所以他老兄就手動幫他建立了 `C:\Users\YOURUSERNAME\AppData\Local\Temp\plugman\git\`


```bash
'C:\Users\YOURUSERNAME\AppD
ata\Local\Temp\plugman\git\1404399211161'.: No such file or directory
```


然後再執行一次...
```bash
H:\Dropbox\project\mobileapp>ionic start newapp tabs
Running start task...
Creating Ionic app in folder H:\Dropbox\project\mobileapp\newapp based on
 tabs project

DOWNLOADING: https://github.com/driftyco/ionic-app-base/archive/master.zip

DOWNLOADING: https://github.com/driftyco/ionic-starter-tabs/archive/master.zip
Initializing cordova project.
Fetching plugin "org.apache.cordova.device" via plugin registry
Fetching plugin "org.apache.cordova.console" via plugin registry
Fetching plugin "https://github.com/driftyco/ionic-plugins-keyboard" via git clo
ne

H:\Dropbox\project\mobileapp>
```


結案，結果 APP 還沒開始寫...(暈

source: [Unable to add plugins. Perhaps your version of Cordova is too old](http://forum.ionicframework.com/t/unable-to-add-plugins-perhaps-your-version-of-cordova-is-too-old/3807/10)