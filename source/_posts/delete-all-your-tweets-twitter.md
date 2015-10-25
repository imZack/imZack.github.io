title: '刪除 Twitter 所有 Tweets (Python)'
date: 2014-07-26 00:15:39
tags:
	- Python
	- Twitter
---

之前就一直很想整理那個亂亂的 [Twitter](https://twitter.com/yuluntw) 帳號，苦無方法一次可以把全部以往訊息刪除的工具！
因為以前都亂連動一些有的沒的社群網路，整個舊有點**髒髒**，上網搜尋到幾個輔助網站也沒啥鳥用！剛好看到這位 Dave Jeffery 寫的小巧 Python Script 來達成任務

{% gist 113241 %}

事前準備
========

1. Twitter App 你可以到這邊[申請](https://apps.twitter.com/)
2. Python 2.5+
3. Tweepy `sudo pip install tweepy`
4. Oauthlib `sudo pip oauthlib`
5. 上面那段 Script `git clone https://gist.github.com/113241.git`


動手開幹
========

接下來把 Script 中的 `CONSUMER_KEY` 與 `CONSUMER_SECRET` 換成你剛剛申請 Twitter APP 的。然後執行 `python delete_all_tweets.py`

他就會出現提示`Authenticate at XXX(你的帳號) and then enter you verification code here > ` 後面會接 oauth 的網址，點下去按授權會拿到一組 verify code，貼上來按 Enter

接著他會確認你是否真的要刪除，輸入 `yes` (大小寫沒差)。之後就會跳出

```
Deleted: XXX
Deleted: XXX
Deleted: XXX
Deleted: XXX
Deleted: XXX
```

等待他執行完畢後，你的帳號就乾乾淨淨囉！

