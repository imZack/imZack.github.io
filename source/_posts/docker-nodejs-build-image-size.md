title: Docker 製作 NodeJS Image 的一些心得
date: 2014-11-17 23:41:31
tags:
- Docker
- NodeJS
---

最近在玩 Docker 來記錄一下製作 NodeJS Image 的過程...

不囉嗦直接看 **Dockerfile**

<!-- more -->

```dockerfile
FROM docker pull dockerfile/nodejs

MAINTAINER Zack YL Shih <zackyl.shih@moxa.com>

ADD . /data
WORKDIR /data

RUN npm install

EXPOSE 8080

CMD npm start
```

我們會發現 Baseimage 搜尋 nodejs 會看到

1. [dockerfile/nodejs](https://registry.hub.docker.com/u/dockerfile/nodejs/)
2. [node](https://registry.hub.docker.com/u/library/node/) **Offical**

一開始沒注意到直接用了 `dockerfile/nodejs` 沒啥大問題，執行起來一切正常唯一有問題的是 image 大小居然高達 **8xx MB**...

## dockerfile/nodejs

底下是他的 Dockerfile，就是去 Node 網站下載最新原始碼並且編譯 (所以需要 based on `dockerfile/python`)，不過這樣的後果是整個 image 無比肥大啊啊啊啊～

```dockerfile
#
# Node.js Dockerfile
#
# https://github.com/dockerfile/nodejs
#

# Pull base image.
FROM dockerfile/python

# Install Node.js
RUN \
  cd /tmp && \
  wget http://nodejs.org/dist/node-latest.tar.gz && \
  tar xvzf node-latest.tar.gz && \
  rm -f node-latest.tar.gz && \
  cd node-v* && \
  ./configure && \
  CXX="g++ -Wno-unused-local-typedefs" make && \
  CXX="g++ -Wno-unused-local-typedefs" make install && \
  cd /tmp && \
  rm -rf /tmp/node-v* && \
  echo '\n# Node.js\nexport PATH="node_modules/.bin:$PATH"' >> /root/.bashrc

# Define working directory.
WORKDIR /data

# Define default command.
CMD ["bash"]
```


## Offical Node

後來改試了 Offical Node 這個 image ...只剩下 **38x MB**

其實他有分很多等級的 **tag**

- 0.10.33, 0.10, 0, latest (0.10/Dockerfile)
- 0.10.33-onbuild, 0.10-onbuild, 0-onbuild, onbuild (0.10/onbuild/Dockerfile)
- 0.10.33-slim, 0.10-slim, 0-slim, **slim** (0.10/slim/Dockerfile)
- 0.11.14, 0.11 (0.11/Dockerfile)
- 0.11.14-onbuild, 0.11-onbuild (0.11/onbuild/Dockerfile)
- 0.11.14-slim, 0.11-slim (0.11/slim/Dockerfile)
- 0.8.28, 0.8 (0.8/Dockerfile)
- 0.8.28-onbuild, 0.8-onbuild (0.8/onbuild/Dockerfile)
- 0.8.28-slim, 0.8-slim (0.8/slim/Dockerfile)

我這邊是直接使用 **node:slim** 因為我的 Application 沒有其他相依性問題要處理

**但是！**如果你的 `npm install` 需要一些 build-essential 以外的東西千萬要注意 `docker build` 的過程中有沒有噴錯，如果有錯的話...那還是要看你**預先編譯好**或是用**空間**來換了...

這部分不知道有沒有更好的解法？

下面是 **slim** 的 Dockerfile，可以看得出來他是直接下載 Binary 然後安裝...

```dockerfile
FROM debian:wheezy

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        build-essential \
        pkg-config \
        git \
        python \
    && rm -rf /var/lib/apt/lists/*

# verify gpg and sha256: http://nodejs.org/dist/v0.10.31/SHASUMS256.txt.asc
# gpg: aka "Timothy J Fontaine (Work) <tj.fontaine@joyent.com>"
RUN gpg --keyserver pgp.mit.edu --recv-keys 7937DFD2AB06298B2293C3187D33FF9D0246406D

ENV NODE_VERSION 0.10.33
ENV NPM_VERSION 2.1.8

RUN curl -SLO "http://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz" \
    && curl -SLO "http://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
    && gpg --verify SHASUMS256.txt.asc \
    && grep " node-v$NODE_VERSION-linux-x64.tar.gz\$" SHASUMS256.txt.asc | sha256sum -c - \
    && tar -xzf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 \
    && rm "node-v$NODE_VERSION-linux-x64.tar.gz" SHASUMS256.txt.asc \
    && npm install -g npm@"$NPM_VERSION" \
    && npm cache clear

CMD [ "node" ]
```

## Dockerfile (simple web application)

最後看一下我的 Dockerfile，主要是啟動 **mosca** 搭配 static folder 掛載 **Sanji MQTT Inspector**

```dockerfile
FROM node:slim

MAINTAINER Zack YL Shih <zackyl.shih@moxa.com>

ADD . /data
WORKDIR /data

RUN npm install

EXPOSE 8080

CMD npm start
```

把當前資料夾加入 `/data` 然後執行指令 `npm install`，並把 8080 port 暴露出去供其他 **Container Linking** 使用

CMD 則直接執行預先寫好的 `npm start` 如下

```bash
#!/bin/sh
node_modules/mosca/bin/mosca -v --http-static . \
    --http-port 8080 \
    --parent-port $BROKER_PORT_1883_TCP_PORT \
    --parent-host $BROKER_PORT_1883_TCP_ADDR
```

其中環境變數 `$BROKER_PORT_1883_TCP_PORT` 與 `$BROKER_PORT_1883_TCP_ADDR`，是透過 **fig.sh** 建立起的連結將此 Container 跟 MQTT Broker 連接所產生的環境變數。這部分下篇文章再來介紹一下。

Stay tuned...