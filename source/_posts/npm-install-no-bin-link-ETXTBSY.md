layout: post
title: 'npm install 錯誤在虛擬機共享資料夾'
date: 2014-06-07 00:32:41
tags:
	- Node.js
	- npm
	- VirtualBox
---

今天更新 Blog 產生器 HEXO 時發現我要安裝 NPM 套件時會發生很詭異的錯誤...

TL;DR
-----
	使用虛擬機分享資料夾，例如我是用 VirtualBox 分享 windows 資料夾給 Debian。系統在建立 Symlink 的時候會有問題。

以下是錯誤訊息

```
npm ERR! error rolling back Error: ETXTBSY, unlink '/media/sf_project/imZack.github.io/node_modul
es/hexo-renderer-marked/node_modules/lodash/lodash.js'
npm ERR! error rolling back  hexo-renderer-marked@0.1.0 { [Error: ETXTBSY, unlink '/media/sf_proj
ect/imZack.github.io/node_modules/hexo-renderer-marked/node_modules/lodash/lodash.js']
npm ERR! error rolling back   errno: 62,
npm ERR! error rolling back   code: 'ETXTBSY',
npm ERR! error rolling back   path: '/media/sf_project/imZack.github.io/node_modules/hexo-rendere
r-marked/node_modules/lodash/lodash.js' }
npm ERR! Error: EROFS, symlink '../marked/bin/marked'
npm ERR! If you need help, you may report this *entire* log,
npm ERR! including the npm and node versions, at:
npm ERR!     <http://github.com/npm/npm/issues>

npm ERR! System Linux 3.2.0-4-amd64
npm ERR! command "/usr/local/bin/node" "/usr/local/bin/npm" "install"
npm ERR! cwd /media/sf_project/imZack.github.io
npm ERR! node -v v0.10.28
npm ERR! npm -v 1.4.9
npm ERR! path ../marked/bin/marked
npm ERR! code EROFS
npm ERR! errno 56
```

丟上去 [google](https://www.google.com.tw/search?q=rolling+back+Error:+ETXTBSY&spell=1&sa=X&ei=v-2RU-OKKs3LlAWVg4DIBg&ved=0CCMQvwUoAA&biw=1858&bih=995)，嗯！我不是孤單的...簡單的抓了一個答案來看得到的結果是：`npm install` 後面加入參數 `--no-bin-link`

依據 [npm 官方文件](https://www.npmjs.org/doc/cli/npm-install.html)表示：
> The --no-bin-links argument will prevent npm from creating symlinks for any binaries the package might contain.

也就是說避免 npm 去幫你產生 symlinks，這時我才忽然想起我的環境是 windows 資料夾透過 virtualbox 的 shared folder 分享到 debian 難怪不能用 symlinks...

這裡似乎有[一篇解法](http://stackoverflow.com/questions/8232778/nodejs-npm-installing-modules-on-ntfs-partition)，將 virtual box 跑在 administrator 下然後在 VM 內設定下面參數
```
VBoxManage setextradata YOURVMNAME VBoxInternal2/
SharedFoldersEnableSymlinksCreate/YOURSHAREFOLDERNAME 1
```

不過我後來還是將這部分回到 windows 上面完成...哈

參考資料
--------
[Nodejs + npm, installing modules on ntfs partition](http://stackoverflow.com/questions/8232778/nodejs-npm-installing-modules-on-ntfs-partition)

[“Error: ETXTBSY” when installing the package with npm (gruntjs, gulp) at VirtualBox](http://stackoverflow.com/questions/23520694/error-etxtbsy-when-installing-the-package-with-npm-gruntjs-gulp-at-virtual)

[NPM, Vagrant and Symlinks on Windows](http://kmile.nl/post/73956428426/npm-vagrant-and-symlinks-on-windows)
