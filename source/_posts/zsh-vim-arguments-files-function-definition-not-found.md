title: ZSH 的 Vim Tab 會壞掉...?
date: 2014-11-28 00:20:09
tags:
  - zsh
  - vim
---

不知道最近吃錯什麼藥，在 ZSH + OH MY ZSH 環境下輸入

**狀況**
```
$ vim (tab)
_arguments:450: _vim_files: function definition file not found
```

WTF，連個檔名自動完成都會壞掉喔....

**解法**
```
$ rm ~/.zcompdump
```

詳細原因是什麼我還不知道，有人知道嗎？

至少現在解決了 XD

來源：[Google](https://www.google.com.tw/search?q=zcompdump&oq=zcompdump&aqs=chrome..69i57j0l2.191j0j1&sourceid=chrome&es_sm=93&ie=UTF-8)