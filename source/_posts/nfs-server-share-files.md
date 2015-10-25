title: 架設 NFS Server 共享檔案 (Debian/Ubuntu)
date: 2014-12-22 21:37:39
tags:
  - nfs
  - linux
---

## Server
`sudo apt-get install nfs-kernel-server`

設定檔簡單設定一下

```plain
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)

/home/zack/Dropbox/project 192.168.1.69(rw,sync,no_subtree_check)
```

## Client

安裝 **nfs-common**
`apt-get install nfs-common`

檢查 host 有開放那些掛載點
`showmount -e 192.168.1.222`

把 222 的 project 掛到本機來
`mount -t nfs 192.168.1.222:/home/zack/project /home/zack/project`


以上基本就搞定囉！
