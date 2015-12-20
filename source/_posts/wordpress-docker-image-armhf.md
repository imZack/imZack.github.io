title: 秒建 WordPress 在你閒置的 Raspberry Pi 上
date: 2015-11-01 00:00:20
tags:
  - Raspberry Pi
  - Docker
  - WordPress
  - Projects
---
最近把吃喝玩樂 Blog 從 Blogspot 搬到 WordPress 了！於是乎就想要玩玩各種功能...

Production 環境目前是 host 在 DigitalOcean ，看了旁邊的 Raspberry Pi 一眼好像閒著也是閒著，於是上網看看有沒有現成的 Docker Image 可以用，結果找不到喜歡的。就自己 Build 一個吧！

英文說明與 Dockerfile, Scripts 都在 Github 連結：[imZack/wordpress-armhf](https://github.com/imZack/wordpress-armhf)

這邊主要會用到兩個 Image 分別是 `zack/wordpress-armhf:4.3.1-apache` 與 `armbuilds/mariadb`，詳細的 Dockerfile 可以在上面 Github 連結裡面找到。由於 Docker Hub 無法自動 Build armhf 的 Image 所以我必須先在 Raspberry Pi 上 build 好然後再推上去。(另有用 QEMU 方式，這邊先不多提...)

```yaml
wordpress:
    image: zack/wordpress-armhf:4.3.1-apache
    restart: always
    links:
     - mysql
    environment:
     - WORDPRESS_DB_PASSWORD=wordpressmeetsdocker
    ports:
     - "8080:80"
    volumes:
     - ./www-data:/var/www/html

mysql:
    image: armbuilds/mariadb
    restart: always
    volumes:
     - ./mysql-data:/var/lib/mysql
    environment:
     - MYSQL_ROOT_PASSWORD=wordpressmeetsdocker
     - MYSQL_DATABASE=wordpress
```

用上面的 docker-compose.yml 就能在幾秒內建立一個全新的 WordPress 環境囉！

只需要下 `docker-compose up` 等他跑完初始化，就能開啟 http://localhost:8080 Port 囉 ！
