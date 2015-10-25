title: Docker 運行在 ARMv7 (MOXA UC8100)
date: 2014-12-19 23:20:56
tags:
  - Docker
  - Debian
  - Ubuntu
  - ArchLinux
  - Fedora
---

暨上週參加完 [Container Summit](/2014/docker-container-summit-2014-ithome-note) 後，這天下班前忽然手癢把之前還沒搞定的 Docker on ARMv7 (Debian 7.7 Wheezy) 再玩一遍...

<!-- more -->


## 環境描述

[Moxa UC-8100](http://www.moxa.com/product/UC-8100.htm)

- CPU: ARMv7 Cortex-A8 300 MHz processor
- Kernel: 3.2.0
- OS: Debian 7.7 Wheezy
- RAM: 256 MB
- Storage
  - 內建 803 MB (剩餘 400 MB)
  - USB 隨身碟 16G (Docker 安裝於此，如何更改 Docker 預設路徑可參考上次的[筆記](/2014/change-docker-working-directory))

```bash
root@Moxa:/home/moxa# uname -a
Linux Moxa 3.2.0-uc8100 #75 Thu Dec 18 19:34:11 CST 2014 armv7l GNU/Linux
```

## 安裝過程

### 安裝 docker.io package
套件 [docker.io](https://packages.debian.org/search?searchon=names&keywords=docker.io)，目前存在於 sid 與 jessie 中。stable 的 wheezy 沒有支援，所以我選擇直接把 sid 加到 source.list 來安裝。

    Package docker.io
      jessie (testing) (admin): Linux container runtime 
        1.3.2~dfsg1-1: amd64 armel armhf i386
      sid (unstable) (admin): Linux container runtime 
        1.3.3~dfsg1-1: amd64 armel armhf i386

我們可以用 `/apt-cache policy docker.io` 看目前 source.list 中有那些存在這個 package，嗯...就是 sid (顯示為多此一舉)

    λ ~/ apt-cache policy docker.io
    docker.io:
      Installed: (none)
      Candidate: 1.3.3~dfsg1-1
      Version table:
         1.3.3~dfsg1-1 0
            500 http://ftp.debian.org/debian/ sid/main armhf Packages
            100 /var/lib/dpkg/status

最後，安裝直接下 `apt-get -t unstable install docker.io`

### 下載 docker image

**注意；**一定要下載 arm build 的 image 喔！不要異想天開把 x86_64 的 image 來用。Docker 只是 kernel 往上的虛擬化技術，不像 QEMU 之類的可以模擬硬體。

這邊有幾個 image 給大家參考，我看大部分都是使用 **Raspberrypi** 的玩家製作的。(反正大家都是 ARMHF 就都可以用啦！)

[Armbuild Org](https://registry.hub.docker.com/repos/armbuild/) 這是一家提供 arm-based server 的法國公司，製作很多 arm build 的 image。

以下是我安裝過的 images 都可以用，其中 Fedora 不知道在肥什麼的...?

    REPOSITORY                   TAG       CREATED      VIRTUAL SIZE
    armbuild/archlinux-disk      latest    4 days ago   436.7 MB
    armbuild/fedora-qcow-minimal 20        11 days ago  1.017 GB
    armbuild/fedora-qcow-minimal heisenbug 11 days ago  1.017 GB
    armbuild/fedora-qcow-minimal latest    11 days ago  1.017 GB
    ygrenzinger/archlinux-arm    latest    5 weeks ago  736.4 MB
    armbuild/debian              latest    8 weeks ago  74.13 MB
    armbuild/debian              wheezy    8 weeks ago  74.13 MB
    mazzolino/armhf-ubuntu       14.04     11 weeks ago 291.5 MB
    mazzolino/armhf-ubuntu       latest    11 weeks ago 291.5 MB
    mazzolino/armhf-ubuntu       trusty    11 weeks ago 291.5 MB


順利地安裝完畢 docker.io 也拉好了 images，滿心期待終於可以在這麼小 **CPU 300 MHZ + 256 MB** 的機器上玩 container ！結果...

#### 錯誤1: 掛載點沒有賦予執行權限

    2014/08/04 10:28:07 Error response from daemon: Cannot start container 281818d1e14ed9b304g85753017ee9c6d0a43d327686262425cd0731a1b1998b: fork/exec /media/1f193829-fe86-4444-b1e7-2538848b1d83/docker/init/dockerinit-1.3.2: permission denied

先用 `umount` 把掛載點卸下，然後 `mount -o exec` 重新掛載起來即可

#### 錯誤2: Kernel LXC 功能沒有(完全)開啟

`docker cgroup is not mounted`

完全沒想到會有這個錯誤，因為我認為 docker.io package 都裝好了，怎麼會是這個原因...難道 postinstall 都不會 check 環境嗎...囧rz

可以先執行 `lxc-checkconfig`，以下是輸出：

```plain
root@Moxa:/home/moxa# lxc-checkconfig 
--- Namespaces ---
Namespaces: enabled
Utsname namespace: enabled
Ipc namespace: enabled
Pid namespace: enabled
User namespace: enabled
Network namespace: enabled
Multiple /dev/pts instances: enabled

--- Control groups ---
Cgroup: enabled
Cgroup clone_children flag: enabled
Cgroup device: enabled
Cgroup sched: enabled
Cgroup cpu account: enabled
Cgroup memory controller: enabled

--- Misc ---
Veth pair device: enabled
Macvlan: enabled
Vlan: enabled
File capabilities: enabled

Note : Before booting a new kernel, you can check its configuration
usage : CONFIG=/path/to/config /usr/bin/lxc-checkconfig
```

原本我的 `Cgroup`, `Veth pair device`, `Macvlan` 是沒有開啟的，而這些是啟用 container 技術的必須前提，所以就請同事幫我**重編一個新的 kernel 啦**！(真好，如果不是在硬體廠我就要自己搞了！XDDDDDD)。

## 實際測試

經過前面的重重關卡終於到了測試時間...

- armbuild/fedora-qcow-minimal:20
- armbuild/fedora-qcow-minimal:20
- mazzolino/armhf-ubuntu:14.04
- armbuild/debian:latest

上面這幾個 image 都可以正常執行，於是我又開始測試 device node 有沒有辦法透通

沒想到只要透過指令 [`--device`](https://docs.docker.com/reference/commandline/cli/) 就可以了

> `--device=[]` Add a host device to the container

於是我就用 `/dev/tty05:/ttyM0` 把 host 的 serial port 導到裡面 ttyM0 ，經過測試一切正常！哈哈～

結束了一個玩轉 Container on Embedded System 上的成就 check!!!

目前看到 https://resin.io/ 好像很猛，還有前面的 http://labs.online.net/

有任何應用歡迎提供阿...!?
