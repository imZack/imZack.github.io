title: 'Node.js npm 安裝 node-sqlite3 套件在不同平台'
date: 2014-06-10 22:28:26
tags:
	- Node.js
	- npm
	- crosscompile
---

今天在公司要跨平台編譯 [node-sqlite3](https://github.com/mapbox/node-sqlite3)，結果編出來的東西一直有問題...
搞半天原來是因為少下了一個指令，囧

在 node 在 0.8 版本之後使用了 [node-gyp](https://github.com/TooTallNate/node-gyp)，可以讓工程師比較輕鬆地將一些 native code 編譯到不同平台上。

>node-gyp is a cross-platform command-line tool written in Node.js for compiling native addon modules for Node.js, which takes away the pain of dealing with the various differences in build platforms. It is the replacement to the node-waf program which is removed for node v0.8. If you have a native addon for node that still has a wscript file, then you should definitely add a binding.gyp file to support the latest versions of node.

由於小弟我知識淺薄，說到 cross-compile 大概只想到置換環境變數 `$(CC)` 或是改改 header 檔案，結果遇到 `npm install` 一時之間還真的不知道要怎麼指定 toolchain 囧！後來翻到了 [node-sqlite3](https://github.com/mapbox/node-sqlite3) 說明中有教你怎麼樣編譯到別的平台上。

我自己的做法
============

首先要具備 node-gyp 並安裝到全域 `npm install node-gyp -g`

再來就是 `npm install sqlite3 --build-from-source --target_arch=arm`

**注意**
這個 `--build-from-source` 非常重要，如果你的 toolchain 是自備的一定要加上這個 flag 要不然他會很好心的幫你直接下載 **arm** 版本的 binary，而達不到你要的效果(cross-compile)

另外，`node-sqlite3`是有套件相依性的...`apt-get install libsqlite3` 可以輕鬆解決

如果想查看他有相依那些 lib 的話不妨來一下 [ldd](http://unixhelp.ed.ac.uk/CGI/man-cgi?ldd+1) 唄！