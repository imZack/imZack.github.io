title: '將 Debian/Ubuntu 套件清單輸出備份與還原'
date: 2014-06-12 00:02:42
tags:
	- Debian
	- Ubuntu
	- Linux
---

要打包系統所以要把目前裝過的套件全部記錄起來

簡單的拜了一下 Google 大神

```bash
dpkg --get-selections | grep -v deinstall
```

輸出已安裝清單
--------------

```bash
	tasksel                                         install
	tasksel-data                                    install
	tcpd                                            install
	tcpdump                                         install
	tmux                                            install
	traceroute                                      install
	ttf-dejavu-core                                 install
	tzdata                                          install
	ubuntu-keyring                                  install
	ubuntu-minimal                                  install
	ucf                                             install
	udev                                            install
	unattended-upgrades                             install
	........................skip...........................
	vnstat                                          install
	vsftpd                                          install
	w3m                                             install
	webalizer                                       install
	wget                                            install
	whiptail                                        install
	whois                                           install
	wireless-regdb                                  install
	x11-common                                      install
	xauth                                           install
	xfonts-encodings                                install
	xfonts-utils                                    install
	xkb-data                                        install
	xml-core                                        install
	xz-lzma                                         install
	xz-utils                                        install
	zlib1g                                          install
	zlib1g-dev                                      install
	zsh                                             install
```

所以說你想要備份目前機器上所有 package 清單就可以輸入下列指令

備份已安裝清單
--------------

```bash
dpkg --get-selections > pkglist.txt
```

還原已安裝套件
--------------

```bash
dpkg --clear-selections
sudo dpkg --set-selections < list.txt
```

source: [How to list all installed packages?](http://askubuntu.com/questions/17823/how-to-list-all-installed-packages)