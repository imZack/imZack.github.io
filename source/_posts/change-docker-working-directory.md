title: 更改 Docker 預設執行位置
date: 2014-09-21 22:46:01
tags:
	- docker
---

這兩天架了 **ELK Stack**(Elasticsearch、Logstash、Kibana) 在當 Log aggregation 的工作，於是就使用 Docker 的 Logstacsh container 來跑。不過就在這個時候發現 Docker 的 Working Directory 空間不夠用！

<!-- more -->

預設 image 會存在 `/var/lib/docker` 要更改他的位置其實很簡單

可以參考本篇 StackOverflow: [How to change the docker image installation directory?](http://stackoverflow.com/questions/24309526/how-to-change-the-docker-image-installation-directory)

啟動 docker service 的時候輸入選項 `-g`，詳細使用方式可使用 `docker --help` 說明。另外也可以設定 `/etc/default/docker` 這個檔案，讓每次啟動自動就能帶入此選項。

```bash
# Docker Upstart and SysVinit configuration file

# Customize location of Docker binary (especially for development testing).
#DOCKER="/usr/local/bin/docker"

# Use DOCKER_OPTS to modify the daemon startup options.
DOCKER_OPTS="-dns 8.8.8.8 -dns 8.8.4.4 -g /mnt"

# If you need Docker to use an HTTP proxy, it can also be specified here.
#export http_proxy="http://127.0.0.1:3128/"

# This is also a handy place to tweak where Docker's temporary files go.
#export TMPDIR="/mnt/bigdrive/docker-tmp"
```

上面的 `DOCKER_OPTS="-dns 8.8.8.8 -dns 8.8.4.4 -g /mnt"` 就是修改的地方，另外還設定了兩個 **DNS** 位置。

如此一來就能更改 docker 的位置囉！
