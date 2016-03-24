title: 比對兩個資料夾下的檔並輸出彩色 diff
date: 2016-03-24 20:42:14
tags:
- Linux
- Ubuntu
---

平常都在 git repo 內做 diff，一時之間不知道要怎麼比對兩個資料夾底下的檔案

這邊作一下紀錄...

```sh
λ ~/temp/ diff -bur FOLDER_1 FOLDER_2
```

會發現輸出的 diff **居然沒有顏色**...

於是乎又找到 `colofdiff`

直接 `apt-get install colordiff` 即可，就會變成這樣

```sh
diff -bur FOLDER_1 FOLDER_2 | colordiff

// 存起來
diff -bur FOLDER_1 FOLDER_2 | colordiff > your.diff
```

打完收工。
