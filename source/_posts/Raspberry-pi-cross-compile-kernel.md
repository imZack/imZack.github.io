title: "Raspberry Pi 樹莓派編譯新核心"
date: 2015-04-27 22:29:57
tags:
  - Raspberry Pi
  - kernel
  - cross-compile
  - linux
---

前陣子在家門口踢到一個...

  ![](https://igcdn-photos-a-a.akamaihd.net/hphotos-ak-xaf1/t51.2885-15/11123682_366899226847560_1223930663_n.jpg)

Raspberry Pi 二代目 Model B，然後就 ~~過著開開心心的生活~~ 開始想把一堆東西裝上去然後~~不知道要幹嘛!?~~書房的 Hub 滿了，所以我就要拿出之前買的 [小米隨身WiFi](http://www.mi.com/tw/miwifi/) 拿出來插上...

就會發現，什麼事都沒有發生。因為...Driver 沒有進 Kernel 根據非官方 [Github 上的 Repo](https://github.com/porjo/mt7601) 顯示：
> This repository is based on the original driver released by MediaTek which was rejected from Linux kernel because of the poor code quality.

嗯！所以展開自編之路...

<!-- more -->

故事一波三折，曲折離奇...先進入正題

## 準備環境

我的環境是 Ubuntu 64bit
```shell
λ ~/uname -a
Linux ubuntu 3.13.0-35-generic #62-Ubuntu SMP Fri Aug 15 01:58:42 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
```

創一個資料夾來放等等要下載的東西
```shell
λ ~/mkdir ~/rasp
```

**注意** 以下的 git clone 會帶  `--depth 1` 表示我們只抓**預設 branch** 的最後一個 commit 這是為了加快下載速度以及節省空間

## Raspberry Pi Toolchain (download)
```shell
λ ~/rasp/ git clone https://github.com/raspberrypi/tools --depth 1
```

toolchain 位於下面位置
```shell
λ ~/rasp/ ls tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-*
tools/../../bin/arm-linux-gnueabihf-addr2line
tools/../../bin/arm-linux-gnueabihf-ar
tools/../../bin/arm-linux-gnueabihf-as
tools/../../bin/arm-linux-gnueabihf-c++
tools/../../bin/arm-linux-gnueabihf-c++filt
tools/../../bin/arm-linux-gnueabihf-cpp
tools/../../bin/arm-linux-gnueabihf-ct-ng.config
tools/../../bin/arm-linux-gnueabihf-dwp
tools/../../bin/arm-linux-gnueabihf-elfedit
tools/../../bin/arm-linux-gnueabihf-g++
tools/../../bin/arm-linux-gnueabihf-gcc
tools/../../bin/arm-linux-gnueabihf-gcc-4.8.3
tools/../../bin/arm-linux-gnueabihf-gcc-ar
tools/../../bin/arm-linux-gnueabihf-gcc-nm
tools/../../bin/arm-linux-gnueabihf-gcc-ranlib
tools/../../bin/arm-linux-gnueabihf-gcov
tools/../../bin/arm-linux-gnueabihf-gdb
tools/../../bin/arm-linux-gnueabihf-gfortran
tools/../../bin/arm-linux-gnueabihf-gprof
tools/../../bin/arm-linux-gnueabihf-ld
tools/../../bin/arm-linux-gnueabihf-ld.bfd
tools/../../bin/arm-linux-gnueabihf-ldd
tools/../../bin/arm-linux-gnueabihf-ld.gold
tools/../../bin/arm-linux-gnueabihf-nm
tools/../../bin/arm-linux-gnueabihf-objcopy
tools/../../bin/arm-linux-gnueabihf-objdump
tools/../../bin/arm-linux-gnueabihf-pkg-config
tools/../../bin/arm-linux-gnueabihf-pkg-config-real
tools/../../bin/arm-linux-gnueabihf-ranlib
tools/../../bin/arm-linux-gnueabihf-readelf
tools/../../bin/arm-linux-gnueabihf-size
tools/../../bin/arm-linux-gnueabihf-strings
tools/../../bin/arm-linux-gnueabihf-strip
```

這邊我選用 **gcc-linaro-arm-linux-gnueabihf-raspbian-x64** 作為我待會要 cross compile 的 toolchain

## Raspberry Pi Kernel (cross-compile + update)
```shell
λ ~/rasp/ git clone https://github.com/raspberrypi/linux --depth 1
```

進入資料夾後依序執行

1.先把建置環境清乾淨
```shell
λ ~/rasp/linux/ make mrproper
```

2.把機器上現有的 Kernel Config 倒出來 (在板子上執行)
```shell
# zcat /proc/config.gz > .config
```
然後再把這個 **.config** 檔案傳回 **linux 資料夾**底下

3.再來先設定好 `CROSS_COMPILE` 環境變數後，執行 `make oldconfig`
```shell
λ ~/rasp/linux/ export CROSS_COMPILE=~/rasp/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-
λ ~/rasp/linux/ ARCH=arm make oldconfig
```
PS1. 這邊 ARCH 記得要指定為 arm 不然會跑到你本機的架構 (ex: x86)
PS2. 執行 oldconfig 有可能會跳出一些選項要你選擇，是因為先前的 Kernel 沒有這些 options 新版本可能會有差異

4.最後執行 `make`
```shell
λ ~/rasp/linux/ ARCH=arm make
```

TicToc~TicToc 跑完後你就有全新的 Kernel 啦！

產出的檔案在 **arch/arm/boot/Image**

```shell
λ ~/rasp/linux/ ls arch/arm/boot/Image -alh
-rwxrwxr-x 1 zack zack 8.1M Apr 27 21:03 arch/arm/boot/Image
```

複製到機器上，`/boot/kernel_new.img` 然後修改 `/boot/config.txt` (怎麼會用附檔名 txt 呀好詭異 XDDD)，加入一行 `kernel=kernel_new.img` ，原本的 kernel 先改個名子放旁邊。
```shell
root@raspberrypi:/boot# cat config.txt
kernel=kernel_new.img
```

再來回到桌機這邊準備一下新的 Kernel modules
```shell
λ ~/rasp/ mkdir modules
λ ~/rasp/linux/ ARCH=arm INSTALL_MOD_PATH=~/rasp/modules make modules_install
```
新版本的 modules 就會跑到 `~/rasp/modules` 囉，把它們全部 SCP 到板子上的 **/lib** 取代
(如果擔心玩壞可以先備份)

萬事搞定，直接下 Reboot 就可以看到裝著新的 Kernel 的 Raspberry Pi 啦~~~~~

```shell
root@raspberrypi:/boot# dmesg
[    0.000000] Booting Linux on physical CPU 0xf00
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.18.11-v7+ (zack@ubuntu) (gcc version 4.8.3 20140303 (prerelease) (crosstool-NG linaro-1.13.1+bzr2650 - Linaro GCC 2014.0
3) ) #1 SMP PREEMPT Mon Apr 27 21:03:05 CST 2015
[    0.000000] CPU: ARMv7 Processor [410fc075] revision 5 (ARMv7), cr=10c5387d
[    0.000000] CPU: PIPT / VIPT nonaliasing data cache, VIPT aliasing instruction cache
[    0.000000] Machine: BCM2709
[    0.000000] cma: Reserved 8 MiB at 0x3d800000
[    0.000000] Memory policy: Data cache writealloc
[    0.000000] On node 0 totalpages: 253952
[    0.000000] free_area_init_node: node 0, pgdat 8080f480, node_mem_map bd033000
[    0.000000]   Normal zone: 1984 pages used for memmap
[    0.000000]   Normal zone: 0 pages reserved
[    0.000000]   Normal zone: 253952 pages, LIFO batch:31
[    0.000000] [bcm2709_smp_init_cpus] enter (8620->f3003010)
[    0.000000] [bcm2709_smp_init_cpus] ncores=4
[    0.000000] PERCPU: Embedded 10 pages/cpu @bd006000 s11456 r8192 d21312 u40960

```

# 參考資料

- [官方說明文件(其實已經很清楚了)](http://elinux.org/Raspberry_Pi_Kernel_Compilation)
