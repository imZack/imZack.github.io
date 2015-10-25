title: '在 Samba 分享資料夾上編輯檔案變成可執行權限'
date: 2014-06-12 23:27:22
tags:
	- Samba
	- Linux
	- Sublime Text
---

因為工作關係桌機使用 Windows 然後檔案透過 Samba 分享在 Linux 環境當中...

但是說也奇怪，每次我使用 Sublime Text 3 編輯完一個檔案存檔後都會變成可執行的權限 **Permission 755**

起初我一直懷疑是 Sublime Text 3 是不是給我動什麼手腳，後來才看到原來有人跟我有一樣的[問題](https://www.sublimetext.com/forum/viewtopic.php?f=3&t=7969)！！！

居然是不起眼的 Samba 在作祟

問題就在於 `smb.conf` 中的參數設定

```bash
map archive = false 
```

要設定成 `false` 才行，預設是 `true`。來看看官方文件怎麼說...

>This controls whether the DOS archive attribute should be mapped to the UNIX owner execute bit. The DOS archive bit is set when a file has been modified since its last backup. One motivation for this option is to keep Samba/your PC from making any file it touches from becoming executable under UNIX. **This can be quite annoying for shared source code, documents, etc...**

原來就是這個**好意**的設定讓我困擾這麼久...