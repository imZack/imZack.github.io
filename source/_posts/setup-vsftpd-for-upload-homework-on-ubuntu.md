title: '用 vsFTPd 架設作業上傳環境 (以 Ubuntu 為例)'
tags:
  - ftp
  - ubuntu
  - vsftpd
id: 76
categories:
  - Linux
date: 2013-05-26 17:48:21
---

請先安裝 [vsFTPd](https://security.appspot.com/vsftpd.html) 方法應該不用多說，[Google ](https://www.google.com.tw/search?safe=off&amp;q=vsftpd+ubuntu&amp;oq=vsftp+ubunt&amp;gs_l=serp.3.0.0i10l2j0i10i30l8.108060.108945.0.109849.6.6.0.0.0.0.68.313.6.6.0...0.0.0..1c.1.12.serp.c2LI6k_pQuU)有很多很多很多

我比較在意的是如何設定讓使用者符合下列行為：

*   **只能上傳 **<span style="font-size: 1em;">(能上傳才能交作業嘛)</span>
*   **不能下載 **<span style="font-size: 1em;">(參考看別人作業)</span>
*   **不能刪除 **<span style="font-size: 1em;">(errr...</span>**誤**<span style="font-size: 1em;">刪別人作業)</span>
*   **除了交作業帳號外其他帳號不受影響** (要不然我也不能用啦)
<!--more-->

一開始我一直嘗試有關使用者權限設定方面著手 (chmod, chgrp) 等等指令...都找不找到理想解決方法 (大概是我太弱了...)
弄了老半天後才發現其實可以用 vsFTPd 設定檔內的功能來達成我上述四點目標

1.  <span style="font-size: 1em;">先開啟 vsftpd 設定檔<span class="lang:sh decode:true crayon-inline">vim /etc/vsftpd.conf</span><span style="font-size: 1em;"> </span>加入<span class="lang:default decode:true  crayon-inline">user_config_dir=/etc/vsftpd/vsftpd_user_conf</span><span style="font-size: 1em;"> ，然後存檔離開。</span></span>
2.  <span class="lang:sh decode:true crayon-inline">vim /etc/vsftpd/vsftpd_user_conf/username</span>建立使用者 (username) 專屬的設定檔加入這段<span class="lang:sh decode:true crayon-inline">cmds_denied=DELE,RNFR,RNTO,RMD,RETR,MKD</span> ，顧名思義就是設定那些指令是不可被執行的。<span style="font-size: 1em;">
</span>
3.  最後重新啟動 vsftpd<span class="lang:sh decode:true  crayon-inline">/etc/init.d/vsftpd restart</span>

<span style="font-size: 1em;">以上，重新登入立馬見效，祝大家交作業愉快。</span>

&nbsp;

## 附錄 FTP 常用指令

`

ABOR - abort a file transfer
CWD - change working directory
DELE - delete a remote file
LIST - list remote files
MDTM - return the modification time of a file
MKD - make a remote directory
NLST - name list of remote directory
PASS - send password
PASV - enter passive mode
PORT - open a data port
PWD - print working directory
QUIT - terminate the connection
RETR - retrieve a remote file
RMD - remove a remote directory
RNFR - rename from
RNTO - rename to
SITE - site-specific commands
SIZE - return the size of a file
STOR - store a file on the remote host
TYPE - set transfer type
USER - send username

`

## 參考資料

[FTP 指令](http://www.nsftools.com/tips/RawFTP.htm) 真正要完整的 FTP protocol 請自行參考 RFC

[vsFTPd 設定檔攻略](http://vsftpd.beasts.org/vsftpd_conf.html) 設定文件, 範例設定檔沒有包含裡面全部

[跟我有類似需求的同學](http://ubuntuforums.org/archive/index.php/t-1605574.html) 謝謝他的發問與後面的回覆

&nbsp;

&nbsp;