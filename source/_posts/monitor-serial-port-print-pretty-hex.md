title: 即時監看 Serail Port 傳輸內容
date: 2017-11-30 22:55:01
tags:
- linux
- socat
- serial
---

有時候要錄機器 Serial Port 的訊息 (Serial Sniffer)，把它印成 HEX 還要有 Timestamp

找了一下網路上大家怎麼做，這邊提供兩種作法

## socat
記得先安裝 socat `apt install socat`

執行結果
```shell
root@Moxa:~# socat -u -x file:/dev/ttyM0,raw file:/dev/null 2>&1 |  awk '{print strftime("%Y-%m-%d %H:%M:%S"), $0; fflush(); }'
2016-02-03 11:18:56  31 32 33 0d 0a
2016-02-03 11:19:04  34 35 36 0d 0a
2016-02-03 11:19:12  37 38 39 0d 0a
```
ref: [Is there a Unix utility to prepend timestamps to stdin?
](http://stackoverflow.com/a/21620)

## jpnevulator

[Debian Package](https://packages.debian.org/unstable/comm/jpnevulator)

```shell
root@Moxa:~# jpnevulator --ascii --timing-print --tty /dev/ttyM0 --read
2016-02-04 02:16:00.178132:
37 38 39 0D 0A                                  789..
2016-02-04 02:16:06.654201:
35 35 35 35 35 35 35 35 35 35 35 35 35 35 35 35 5555555555555555
35 35 35 0D 0A                                  555..
2016-02-04 02:16:16.902654:
35 35 35 35 35 35 35 35 35 35 35 35 35 35 35 35 5555555555555555
61 73 64 66 61 73 64 66 61 35 35 35 0D 0A       asdfasdfa555..
2016-02-04 02:16:26.734887:
41 6E 64 79 7E 7E 7E 7E 7E 7E 7E 7E 7E 7E 7E 7E Andy~~~~~~~~~~~~
7E 7E 7E 7E 7E 7E 7E 7E 7E 7E 48 65 6C 6C 6F 0D ~~~~~~~~~~Hello.
0A
```

ref: [](https://jpnevulator.snarl.nl/)


以上兩種方式，任君選擇～
