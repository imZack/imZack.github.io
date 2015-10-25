title: "編譯小米 WIFI (mt7601) 給樹莓派使用"
date: 2015-04-28 23:06:43
tags:
  - Raspberry Pi
  - linux
  - wireless
  - mt7601
  - cross-compile
  - kernel module
  - driver
---

## 安裝 Driver
繼上一篇[自編 Kernel](/2015/Raspberry-pi-cross-compile-kernel/) 後，其實我最主要的目的是希望把小米 WiFi 的 Driver 裝上...

下載我先前修改過([加入MIFI 的 usb device id](https://github.com/imZack/mt7601/commit/13e5067c8f20f35363f28b653e9bba8d11601ba1))
```shell
λ ~/git clone https://github.com/imZack/mt7601.git
```
<!-- more -->

然後再設定好 `LINUX_SRC`、`CROSS_COMPILE` 兩個參數
```shell
λ ~/cd mt7601/src
λ ~/export LINUX_SRC=~/rasp/linux
λ ~/export CROSS_COMPILE=~/rasp/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-
```

最後執行
```shell
λ ~/make
```

產出的檔案有兩個分別是

**os/linux/mt7601Usta.ko** 及 **RT2870STA.dat**

把這兩個 scp 到 Raspberry Pi 上

```
scp os/linux/mt7601Usta.ko RT2870STA.dat 192.168.1.130:/tmp
```
然後在機器上搬到正確的位置 **記得要用 root **
```
# mv /tmp/mt7601Usta.ko /lib/modules/3.18.11-v7+/kernel/net/wireless/mt7601Usta.ko
# mv /tmp/RT2870STA.dat /etc/Wireless/RT2870STA/RT2870STA.dat
```

最後就執行
```
# depmod -a
# modprobe mt7601Usta.ko
# dmesg
[  242.349499] usbcore: registered new interface driver rt2870
```
可以看見他已經正確被載入了

## 將 ra0 改為常見的 wlan0
搞定後，插上 WiFi dongle 會發現他變成 **ra0** 而不是 **wlan0** 有些軟體可能不能用。所以我在這邊修改

```shell
# vim /etc/udev/rules.d/70-persistent-net.rules
```
在這個檔案最後一行加入
```shell
ACTION=="add", SUBSYSTEM=="net", ATTR{type}=="1", KERNEL=="ra*", NAME="wlan0"
```

重新開機讓他生效
```shell
# reboot
```

## 設定 wpa_supplicant.conf
```shell
# vim /etc/wpa_supplicant/wpa_supplicant.conf
```

我的 ssid 為 zack 密碼為 123456789

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
    ssid="zack"
    psk="123456789"
}
```
詳細說明 [wpa_supplicant.conf](http://w1.fi/cgit/hostap/plain/wpa_supplicant/wpa_supplicant.conf)


## 設定一開機自動連上網
```shell
# vim /etc/network/interfaces
```

主要看下面的 **wlan0** 部分，這樣開機就會自動連線並且啟動 dhclient 取得 IP

```shell
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

iface default inet dhcp
```

設定完後不需要重新開機，直接執行 `ifdown wlan0 && ifup wlan0`

打完收工。
