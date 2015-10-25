title: 自己的 Endpoints 自己保護 (apache2/mod_security)
date: 2014-08-25 23:10:25
tags:
	- api
	- security
	- apache2
---

## 起源

前幾天 Release 了 [Uber Now](http://yulun.me/2014/08/24/Uber-Now-Pebble-watchapp-pick-up-time/)，目的是希望快速地獲得來接你的時間。沒想到我好心提供的 Backend 一直被不明人士每隔五秒鐘就要來幫我**測流量**，導致這篇文章的誕生...

<!-- more -->

上網查了一下，要限制 API Endpoints 關鍵字大約用 `API`, `Rate Limiting` 之類的去查可以發現一些解法，因為我這次是把用 Node 寫的跑在 Apache2 後面，所以我找了一個 apache2 上好像還滿常用的 mod 來玩玩看。

## 安裝 & 設定

```sh
// install using apt-get
sudo apt-get install libapache2-modsecurity
// enable mod
sudo a2enmod mod-security
```

編輯 site-avaliable 中的設定
```sh
sudo vim /etc/apache2/sites-available/uber
```

檔案內容如下
```
<VirtualHost *:80>
        ServerName pebble-uber.yulun.me
        ServerAlias *.pebble-uber.yulun.me
        <Location />
        SecRuleEngine On
        SecAction "phase:1,nolog,initcol:IP=%{REMOTE_ADDR},setvar:IP.pagecount=+1,expirevar:IP.pagecount=60"
        SecRule IP:PAGECOUNT "@gt 10" "phase:1,deny,status:403,msg:'Too many requests'"
        ProxyPass http://localhost:7777/
        ProxyPassReverse http://localhost:7777/
        </Location>
</VirtualHost>
```

從上面可以看到設定設定 使用 `IP=%{REMOTE_ADDR}` ，`setvar:IP.pagecount=+1,expirevar:IP.pagecount=60` 則代表取名變數 pagecount 過期的時間是 60 秒鐘。

`SecRule IP:PAGECOUNT "@gt 10" "phase:1,deny,status:403,msg:'Too many requests'`
上面這行就是說超過 `pagecount > 10` 的話就拒絕訪問，回應 HTTP STATUS 403

這樣一個簡單的保護就完成啦！(記得重啟)

```sh
sudo service apache2 restart
```

**自己的 Endpoint 自己保護** 我們下次見！

## 參考
[Gist](https://gist.github.com/bortzmeyer/2605348)

