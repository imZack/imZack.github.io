title: 架設 tftp server 在 Ubuntu 環境
date: 2016-01-03 23:53:47
tags:
  - Debian
  - Ubuntu
  - Tftp
---

[TFTP (Trivial File Transfer Protocol)](https://en.wikipedia.org/wiki/Trivial_File_Transfer_Protocol)，是很常見的通訊協議用在一些小系統上，如 cisco 韌體更新、或是有些廠家會 BIOS 內建 tftp 下載 rom 檔功能。如此輕巧簡易的通訊協議就很常派上用場。

Reference: [How do I install and run a TFTP server?](http://askubuntu.com/questions/201505/how-do-i-install-and-run-a-tftp-server)

1. 先安裝好相關套件
```
sudo apt-get install xinetd tftpd tftp
```

2. 新增一組設定檔，放置於 `/etc/xinetd.d/tftp`
```
service tftp
{
  protocol        = udp
  port            = 69
  socket_type     = dgram
  wait            = yes
  user            = nobody
  server          = /usr/sbin/in.tftpd
  server_args     = /tftpboot
  disable         = no
}
```

這邊可以看到開啟了 udp 在 69 port、user 是 nobody、server 是 /usr/sbin/in.tftpd、而帶入的參數即為我們放置檔案的資料夾 /tftpboot

3. 新增 /tftpboot 資料夾，並且修改權限
```
sudo mkdir /tftpboot
sudo chmod -R 777 /tftpboot
sudo chown -R nobody /tftpboot
```

4. 將 xinetd 重新啟動
```
sudo service xinetd restart
```

5. 放置一任意檔案，然後用 tftp 測試
```
tftp localhost
tftp> get test
Sent xxx bytes in 0.0 seconds

tftp> quit

cat test
```

大功告成，打完收工。
