title: 依照檔名中版本號碼排序
date: 2015-11-06 20:00:00
tags:
  - shell
---

`ls -v` 可以依照檔名中的版本來排序，根本太強大啊！

**Before -v**

```
root@zack:/unstable# ls i-feel-so-good*.deb -alh

-rw-rw-r-- 1 zack zack 2.2M Sep 14 11:41 i-feel-so-good0.9.0_all.deb
-rw-rw-r-- 1 zack zack 1.7M Oct 13 17:40 i-feel-so-good0.9.10_all.deb
-rw-rw-r-- 1 zack zack 1.9M Oct 29 10:48 i-feel-so-good0.9.11_all.deb
-rw-rw-r-- 1 zack zack 1.8M Sep 22 17:25 i-feel-so-good0.9.1_all.deb
-rw-rw-r-- 1 zack zack 1.8M Sep 22 17:55 i-feel-so-good0.9.2_all.deb
-rw-rw-r-- 1 zack zack 1.8M Sep 24 15:28 i-feel-so-good0.9.3_all.deb
-rw-rw-r-- 1 zack zack 1.8M Sep 30 18:04 i-feel-so-good0.9.5_all.deb
-rw-rw-r-- 1 zack zack 1.8M Sep 30 18:41 i-feel-so-good0.9.6_all.deb
-rw-rw-r-- 1 zack zack 1.8M Oct  2 18:01 i-feel-so-good0.9.7_all.deb
-rw-rw-r-- 1 zack zack 1.7M Oct 13 10:17 i-feel-so-good0.9.8_all.deb
-rw-rw-r-- 1 zack zack 1.7M Oct 13 10:42 i-feel-so-good0.9.9_all.deb
```

**After -v**

```
root@zack:/unstable# ls i-feel-so-good*.deb -avlh
-rw-rw-r-- 1 apt-server apt-server 2.2M Sep 14 11:41 i-feel-so-good0.9.0_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.8M Sep 22 17:25 i-feel-so-good0.9.1_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.8M Sep 22 17:55 i-feel-so-good0.9.2_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.8M Sep 24 15:28 i-feel-so-good0.9.3_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.8M Sep 30 18:04 i-feel-so-good0.9.5_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.8M Sep 30 18:41 i-feel-so-good0.9.6_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.8M Oct  2 18:01 i-feel-so-good0.9.7_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.7M Oct 13 10:17 i-feel-so-good0.9.8_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.7M Oct 13 10:42 i-feel-so-good0.9.9_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.7M Oct 13 17:40 i-feel-so-good0.9.10_all.deb
-rw-rw-r-- 1 apt-server apt-server 1.9M Oct 29 10:48 i-feel-so-good0.9.11_all.deb
```


又學到一招了！
