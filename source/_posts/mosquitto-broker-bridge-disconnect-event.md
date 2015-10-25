title: Mosquitto 如何訂閱 bridge 連線狀態?
date: 2014-12-19 23:40:16
tags:
  - mosquitto
  - mqtt
  - iot
---

一般來說如果要得知 mqtt client 是不是離線了，可以透過 will message 來設定。但是  mosquitto 的貼心功能 bridge 要怎麼實現這個功能呢？

本來看使用說明 `/$SYS` 這部分沒找到，原來藏在 `mosquitto.conf` 的說明文檔中...

**notifications [ true | false ]**
>If set to true, publish notification messages to the local and remote brokers giving information about the state of the bridge connection. Retained messages are published to the topic `$SYS/broker/connection/<clientid>/state` unless otherwise set with notification_topics. If the message is 1 then the connection is active, or 0 if the connection has failed. Defaults to true.

**notification_topic topic**
>Choose the topic on which notifications will be published for this bridge. If not set the messages will be sent on the topic `$SYS/broker/connection/<clientid>/state`.

很明顯可以看出只要訂閱 `$SYS/broker/connection/<clientid>/state` 即可，上線會傳 `0` 反之就是 `1`

如此一來就不用自己做 keepalive 功能啦！

詳細說明：http://mosquitto.org/man/mosquitto-conf-5.html
$SYS/broker 詳細說明：http://mosquitto.org/man/mosquitto-8.html
