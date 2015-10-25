title: 加大 TMUX Buffer 與儲存目前視窗
date: 2014-11-26 00:21:56
tags:
- Tmux
- Linux
---


## 加大 Buffer
最近工作上要開 TMUX 連到小機器上，所以我想要有很大的螢幕，也就是 (Screen Buffer) 要夠大。
很簡單，只要 TMUX 內下指令 `prefix` + `:` 輸入

```
set-option -g history-limit 32768
```

這樣就可以得到一個**非常大**的卷軸啦！往上捲不會？

按 `prefix` + `[` 進入 `copy mode`

## 儲存目前視窗
輸入以下指令到你的 `~/.tmux.conf` 或是直接 `prefix` + `:` 設定

```
bind-key P command-prompt -p 'save history to filename:' -I '~/tmux.history' 'capture-pane -S -32768 ; save-buffer %1 ; delete-buffer'
```

完成後，往後你想要儲存目前視窗(Buffer 所有內容)只要按 `prefix` + `P` 然後輸入儲存路徑 (預設是 `~/tmux.history`) 就可以囉！

Ref:
- http://unix.stackexchange.com/questions/26548/write-all-tmux-scrollback-to-a-file