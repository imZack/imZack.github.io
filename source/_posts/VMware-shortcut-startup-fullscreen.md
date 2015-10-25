title: 快速啟動虛擬機 (建立捷徑 Windows/VMware)
date: 2014-09-01 22:22:10
tags:
	- VMware
---

每次使用 VM 環境時打開的步驟都是

1. 開 VM Player or Virtual
2. 找到你要開的 VM 點兩下
3. 放大全螢幕 或 調整到適當的位置

有鑑於我想長期開始使用 VM 當桌上開發環境，因此特別找一下有沒有方法可以快速開啟並且最大化！(因為我有雙螢幕)

<!-- more -->

VMware
======
以下是 VMware 的捷徑內容
```
"C:\Program Files (x86)\VMware\VMware Workstation\vmware.exe" -X "C:\xxx\Workspace Ubuntu 64bit.vmx"
```


### 語法

**Linux**
```sh
/usr/bin/vmware [-n] [-x] [-X] [-t] [-q] [-s variable_name = value ] [-v] [ path_to_vm .vmx] [http[s]:// path_to_vm .vmx] [X toolkit options]
```

**Windows**
```
C:\Program Files\VMware\VMware Workstation\vmware.exe [-n] [-x] [-X] [-t] [-q] [-s variable_name = value ] [-v] [ path_to_vm .vmx] [http[s]:// path_to_vm .vmx]
```



### 選項說明
```plain
-n Opens a new Workstation window.

-t Opens a virtual machine in a new tab in the existing Workstation window.

-x Powers on the virtual machine when Workstation starts. This option is equivalent to clicking Power On in the Workstation toolbar.

-X Powers on the virtual machine and switches the Workstation window to full screen mode.

-q Closes the virtual machine tab when the virtual machine powers off. If no other virtual machine is open, it also exits Workstation. This option is useful when the guest operating system can power off the virtual machine.

-s Sets the specified variable to the specified value. You can specify at the command line any variable names and values that are valid in the configuration file.

-v Displays the product name, version, and build number.

path_to_vm.vmx Launches a virtual machine by using the specified virtual machine configuration (.vmx) file.

http[s]:// path_to_vm .vmx Stream a virtual machine from a Web server. The virtual machine must be made available for streaming.
```


參考資料
========

1. http://pubs.vmware.com/workstation-10/index.jsp#com.vmware.ws.using.doc/GUID-DA203314-F153-4F1F-8FCF-A7700530943D.html?resultof=%2522%2563%256f%256d%256d%2561%256e%2564%2522%2520

2. http://pubs.vmware.com/workstation-10/index.jsp?topic=%2Fcom.vmware.ws.using.doc%2FGUID-7369457F-FE1D-40FE-97B6-B29CA4916CCD.html