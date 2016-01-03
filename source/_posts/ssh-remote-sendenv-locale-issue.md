title: 關閉 SSH SendENV 解決 locale 問題
date: 2016-01-03 16:59:29
tags:
  - Ssh
  - Linux
---

前幾天遇到一個詭異的問題，就是我使用 ssh 登入遠端 Server，執行特定程式會發生 locale 的問題，看了一下 `locales`...

驚人的事情發生了！因為同事電腦登入過去一樣下 `locales` 居然跟我的不一樣...這是什麼妖術？

原來，預設 ssh 會預設傳送本地端的 env 到遠端 server ，為了解決此問題只要編輯 `/etc/ssh/ssh_config`，把 `SendEnv` 這一行註解掉即可。

原本錯誤訊息可能長這樣：
```
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
        LANGUAGE = (unset),
        LC_ALL = (unset),
        LC_PAPER = "de_DE.UTF-8",
        LC_CTYPE = "de_DE@euro",
        LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
```

或這樣
```
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
```

reference: http://www.jerri.de/blog/archives/2009/12/04/problem_with_locales_on_remote_server_via_ssh/
