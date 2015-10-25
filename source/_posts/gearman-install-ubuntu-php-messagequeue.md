title: 'Gearman 安裝心得 (Ubuntu)'
tags:
  - gearman
  - 'message queue'
  - mysql
  - php
  - ubuntu
id: 111
categories:
  - PHP
date: 2013-05-30 03:36:02
---

## 安裝

先把安裝來源加入
{% codeblock %}
sudo add-apt-repository ppa:gearman-developers/ppa
sudo aptitude update
{% endcodeblock %}
直接下指令安裝
{% codeblock %}
sudo aptitude install gearman
{% endcodeblock %}

<!-- more -->

設定 Persistent Queue 避免 Job server crash 時尚未完成的工作消失

下面是使用 MySQL 的參數，設定資料庫、使用者、密碼，會自動建立表 `gearman_queue`
<pre class="lang:sh decode:true">--queue-type=MySQL --mysql-host=localhost --mysql-port=3306 --mysql-user=username --mysql-password=password --mysql-db=dbname</pre>
這邊我是使用 MySQL 當作 Job Server 儲存尚未完成工作的地方

接著執行
<pre class="lang:sh decode:true">sudo vim /etc/init.d/gearman-job-server</pre>
把前面的參數輸入到
<pre class="lang:sh decode:true">PARAMS="--queue-type=MySQL --mysql-host=localhost --mysql-port=3306 --mysql-user=username --mysql-password=password --mysql-db=dbname"</pre>
重新啟動後就大功告成
<pre class="lang:sh decode:true">sudo service gearman-job-server restart</pre>

## 試用

to be continued.

## 後記

to be continued.

&nbsp;

## 參考資料

[Gearman 心得](http://www.jaceju.net/blog/archives/1211/)

[Compiling Gearman 1.1.3 with MySQL Persistent Storage on Ubuntu 12.04 LTS Precise](http://blog.andyburton.co.uk/index.php/2012-12/compiling-gearman-with-mysql-persistent-storage-on-ubuntu-12-04-precise/)