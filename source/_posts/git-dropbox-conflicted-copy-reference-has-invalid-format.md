title: 由 Dropbox 產生 "衝突的複本" 造成 Git 錯誤
date: 2014-12-12 20:07:11
tags:
  - git
  - Dropbox
---

是說今天參加 [Container Summit]() 回家想要更新網誌推個 code 結果發現悲劇，故事就此展開...

因為我把整個 Project 放在 Dropbox 裡面，然後之前好像是因為網路不穩沒同步導致後來出現一堆 **衝突的副本**檔案！原先不以為意看到一個才砍一個...

結果今天要 Push 的時候就爆炸惹...
```
On branch master
nothing to commit, working directory clean
fatal: Reference has invalid format: 'refs/heads/master (與 YuLun的MacBook Air 衝突的複本 2014-12-07)'
```

一氣之下趕緊把所有這些衝突的檔案給他砍光光
```sh
cd YOUR_DIR
find . -type f -name "* conflicted copy*" -exec rm -f {} \;
```

若還需要刪除 `packed-refs file` 的內容再執行下面指令
```sh
awk '!/conflicted/' .git/packed-refs > temp && mv temp .git/packed-refs
```

打完收工結束這個回合。

PS. **conflicted copy** 是英文版產生的檔案，對應到中文就是 **衝突的副本**

參考資料：[Git fatal: Reference has invalid format: 'refs/heads/master](http://stackoverflow.com/questions/12773488/git-fatal-reference-has-invalid-format-refs-heads-master)

