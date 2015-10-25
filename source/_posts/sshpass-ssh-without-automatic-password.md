title: '利用 sshpass 幫你輸入 ssh 密碼'
date: 2014-05-29 00:19:37
tags:
	- linux
	- note
---

最近公司在測產品的時候常常要使用 scp 傳檔案到機器(target)上

每次都要輸入__密碼__，實在是很惱人阿~ 好險有 sshpass 讓你沒煩惱

只要輸入
	
	sshpass -p your_password scp file root@xxx.xxx.xxx:/home

就可以不用輸入密碼把檔案傳到遠端啦！


如何安裝？

Ubuntu & Debian

	apt-get install sshpass

sshpass: http://www.cyberciti.biz/faq/noninteractive-shell-script-ssh-password-provider/
install: https://gist.github.com/arunoda/7790979