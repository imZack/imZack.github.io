title: Wireshark x ssh x tcpdump 遠端偵錯
date: 2015-12-15 01:21:43
tags:
  - Linux
  - Wireshark
  - 講個秘訣
---

最近工作常常需要用到 **Wireshark** 來看看一些怪怪的問題...

如果是要除錯一般 ethernet 就還好，可以借一台具備 Port Mirror 功能的 Switch 來除錯，但是如果遇到像是 WiFi 或 Cellular 這種無線介面，就只能用 **TCPDUMP** 擋著...

但是，光是從 console 觀察 TCPDUMP 資訊實在很費工夫，好在 Wireshark 有提供從 stdin pipe 進來的機制 (當然，你也可以用 tcpdump 存成檔案在用 Wireshark 來讀取)

於是乎，只要下...

### Linux / Mac
```sh
ssh -l root 192.168.3.127 tcpdump -U -s0 -w - -i eth1 | wireshark -k -i -
```

eth1 換成你要的介面

### Windows (without cygwin)
```cmd
plink.exe -ssh -pw abc123 root@192.168.2.1 "tcpdump -ni eth0 -s 0 -w - not port 22" | "C:\Program Files\Wireshark\Wireshark.exe" -k -i -
```
其中 plink 可前往 putty 下載頁面 [Download]()


Reference:
- [Remote capture via ssh and pipe](https://ask.wireshark.org/questions/23609/remote-capture-via-ssh-and-pipe)
