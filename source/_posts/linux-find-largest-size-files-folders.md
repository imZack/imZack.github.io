title: 'Linux Find 指令找出前幾大的檔案或資料夾'
date: 2014-05-29 00:05:06
tags:
	- linux
	- note
---

找前 10 大的檔案
----------------

```bash
find node_modules -type f -print0 | xargs -0 du | sort -n | tail -10 | cut -f2 | xargs -I{} du -sh {}
```

```bash
265K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/trello.css
271K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/diff.txt
283K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/soundcloud.condensed3b_yui.css
284K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/soundcloud.condensed3b.css
297K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/soundcloud.yui.css
317K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/github.diff
325K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/soundcloud.css
339K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/yahoo.diff
457K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/trello.diff
499K    node_modules/hexo-generator-minify/node_modules/css-condense/extras/soundcloud.diff
```

找前 10 大的資料夾
----------------
```bash
find . -type d -print0 | xargs -0 du | sort -n | tail -10 | cut -f2 | xargs -I{} du -sh {}
```

```bash
1.9M    ./.deploy
3.8M    ./node_modules/hexo-migrator-wordpress/node_modules
3.8M    ./node_modules/hexo-migrator-wordpress
4.4M    ./themes
8.2M    ./node_modules/hexo-generator-minify/node_modules/css-condense/extras
8.7M    ./node_modules/hexo-generator-minify/node_modules/css-condense
9.5M    ./node_modules/hexo-generator-minify/node_modules
9.5M    ./node_modules/hexo-generator-minify
15M     ./node_modules
22M     .
```
差異就在 find 的 `-type d` 跟 `-type f` 分別代表 directory 與 file

如果要變成前 20 大，只要改 `tail -10` 變成 `tail -20`

source: [Linux utility for finding the largest files/directories](http://superuser.com/questions/9847/linux-utility-for-finding-the-largest-files-directories)