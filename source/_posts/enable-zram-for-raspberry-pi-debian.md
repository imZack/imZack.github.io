title: 啟用 zram 在 Raspberry Pi 上
date: 2015-09-08 22:18:21
tags:
  - Raspberry Pi
  - Debian
  - Ubuntu
---

有鑑於 Raspberry Pi 2 記憶體只有 1G，一個不小心可能程式就被 Kernel 發出 OOM 事件幹掉...

這邊就想到 2013 WWDC OS X 狂推猛推的神奇黑科技 (事實上卻是早在 Linux 好一陣子的 zram 技術來用，嘖嘖！)

> zram 有利於嵌入式裝置、小筆電和其它相似的低端硬體裝置。這些裝置通常使用固態儲存，它們由於其原生性質而壽命有限，因而避免以其提供交換空間可防止其迅速磨損。此外，使用zRAM還可顯著降低Linux系統用於交換的I/O

- [zRAM，先前稱為compcache](https://zh.wikipedia.org/zh-tw/Zram)
- [Debian ZRam](https://wiki.debian.org/ZRam)
- [zram: Compressed RAM based block devices](https://www.kernel.org/doc/Documentation/blockdev/zram.txt)

<!-- more -->

先把原本的 swap 關掉 (預設應該是用 dphys-swapfile)
```
sudo update-rc.d dphys-swapfile disable
```

再來查一下 Kernel 有沒有支援 zram module
```
λ ~/ modinfo zram
filename:       /lib/modules/4.2.0-rc7-v7+/kernel/drivers/block/zram/zram.ko
description:    Compressed RAM Block Device
author:         Nitin Gupta <ngupta@vflare.org>
license:        Dual BSD/GPL
srcversion:     BDC5585C607CBB474B00AC9
depends:        zsmalloc,lz4_compress
intree:         Y
vermagic:       4.2.0-rc7-v7+ SMP preempt mod_unload modversions ARMv7
parm:           num_devices:Number of pre-created zram devices (uint)
```

如果有的話應該會上面訊息...

然後就可以來安裝囉～
```
# 下載 script
λ ~/ sudo wget https://gist.githubusercontent.com/imZack/b7aa605a440de442307d/raw/075c09bf1ae5d9903d59dc28af06c7182522a2af/zram.sh -O /etc/init.d/zram

# 給予執行權限
λ ~/ sudo chmod +x /etc/init.d/zram

# 加入開機啟動
λ ~/ sudo update-rc.d zram defaults
```

不用重開機，來測試一下！
```
λ ~/ free
             total       used       free     shared    buffers     cached
Mem:        948068     192872     755196       6732      21996      98104
-/+ buffers/cache:      72772     875296
Swap:            0          0          0
```

接著執行 `sudo service zram start`
```
λ ~/ sudo service zram start
λ ~/ free
             total       used       free     shared    buffers     cached
Mem:        948068     196608     751460       6748      22044      98128
-/+ buffers/cache:      76436     871632
Swap:       948064          0     948064
```

看到 Swap 跑出來就表示大功告成啦！
