title: Rsync 更改預設 SSH Port
date: 2014-12-01 23:38:56
tags:
  - Rsync
  - Linux
---

一般 Rsync 指令如下

`rsync -av /home/zack username@host`

若要更改 SSH PORT (default 22) 的話則可下

`rsync -av -e 'ssh -p 2222' /home/zack username@host`

常常會忘記...紀錄一下


