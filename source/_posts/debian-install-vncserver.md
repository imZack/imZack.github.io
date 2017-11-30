title: 安裝 VNC Server 遠端桌面
date: 2017-11-30 22:21:40
tags:
- Linux
- Ubuntu
- Debian
- VNC
---

最近有遠端桌面需求，就來裝一下 [VNC](https://en.wikipedia.org/wiki/Virtual_Network_Computing)...

> In computing, Virtual Network Computing (VNC) is a graphical desktop sharing system that uses the Remote Frame Buffer protocol (RFB) to remotely control another computer. It transmits the keyboard and mouse events from one computer to another, relaying the graphical screen updates back in the other direction, over a network.

1. Debian 系，直接 `apt update` 更新 source list
2. 直接安裝兩個套件 `apt install tightvncserver autocutsel`
3. 啟動 Copy/Paste 功能，編輯 `~/.vnc/xstartup`。改為下面：
  ```
  #!/bin/sh

  xrdb $HOME/.Xresources
  xsetroot -solid grey
  autocutsel -fork
  #x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
  #x-window-manager &
  # Fix to make GNOME work
  export XKL_XMODMAP_DISABLE=1
  /etc/X11/Xsession
  ```
  - `autocutsel -fork` 是負責[處理 X Server 的 Copy/Paste](http://www.nongnu.org/autocutsel/)

4. 最後可以透過指令 `tightvncserver -geometry 1440x900` 啟動一個解析度為 `1440x900` 的桌面

