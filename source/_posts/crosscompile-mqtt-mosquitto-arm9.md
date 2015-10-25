title: 'MQTT Mosquitto 移植 arm9 平台'
date: 2014-05-16 21:47:03
tags:
- crosscompile
- mqtt
---

![](https://farm8.staticflickr.com/7383/14195728541_ddc3849983_o.png)

今天在公司弄一些 RD Award 的東西，就順手把 [mosquitto](http://mosquitto.org/) 移植到公司產品上

不囉嗦先準備好 ARM9 32-bit RISC CPU toolchain 然後把一些環境變數設定好

建立一個 `build.sh` 內容物如下...

```bash
#!/bin/sh

MACH=arm9 #xscale

if [[ "${MACH}" = xscale ]]; then
	TOOLCHAIN="/usr/local/arm-linux-4.4.2-v4"
	HOST="arm-none-linux-gnueabi"
else
	TOOLCHAIN="/usr/local/arm-linux"
	HOST="arm-linux"
fi

# export shell environment variables
export PATH="${PATH}:${TOOLCHAIN}/bin"
export CPP="${HOST}-gcc -E"
export STRIP="${HOST}-strip"
export OBJCOPY="${HOST}-objcopy"
export AR="${HOST}-ar"
export RANLIB="${HOST}-ranlib"
# This is due to linker doesn't take the 'rpath' parameter
#export LD="${HOST}-ld"
export LD="${HOST}-g++"
export OBJDUMP="${HOST}-objdump"
export CC="${HOST}-gcc"
export CXX="${HOST}-g++"
export NM="${HOST}-nm"
export AS="${HOST}-as"

make clean
make
```

上面這些參數設定基本上就依照不同情況去做一些調整，網路上有些 crosscompile 的文章可以參考看看 (裡面的 MACH 改成  xscal 是我要移植到另一台用的)

Compile 過程當中會遇到兩個錯誤，分別是...

	net_mosq.c: In function `_mosquitto_try_connect':
	net_mosq.c:235: error: `AI_ADDRCONFIG' undeclared (first use in this function)
	net_mosq.c:235: error: (Each undeclared identifier is reported only once
	net_mosq.c:235: error: for each function it appears in.)
	make[1]: *** [net_mosq.o] Error 1

上面這個 AI_ADDRCONFIG 可參考 [RFC 2553](http://www.ietf.org/rfc/rfc2553.txt) 裡面的描述...

>The AI_ADDRCONFIG flag specifies that a query for AAAA records
should occur only if the node has at least one IPv6 source
address configured and a query for A records should occur only
if the node has at least one IPv4 source address configured.

>For example, if the node has no IPv6 source addresses
configured, and af equals AF_INET6, and the node name being
looked up has both AAAA and A records, then:

>(a) if only AI_ADDRCONFIG is specified, the function
returns a NULL pointer;
(b) if AI_ADDRCONFIG | AI_V4MAPPED is specified, the A
records are returned as IPv4-mapped IPv6 addresses;

總之就是 IPv6 的東東，於是我們可以在檔案 `mosquitto/src/lib/net_mosq.h` 69行附近加入

```c
#ifndef AI_ADDRCONFIG
#define AI_ADDRCONFIG 0x0000
#endif
```

大功告成讓我們繼續看下去...

嗯，很好又遇到錯誤了

	net.c:342: error: `IPV6_V6ONLY' undeclared (first use in this function)
	net.c:342: error: (Each undeclared identifier is reported only once
	net.c:342: error: for each function it appears in.)
	make[1]: *** [net.o] Error 1

原來是 IPV6_V6ONLY 沒有定義阿...依據 [google](http://man7.org/linux/man-pages/man7/ipv6.7.html) 所以我們簡單把他設定為一個 nonzero 就可以了

>IPV6_V6ONLY (since Linux 2.4.21 and 2.6)
If this flag is set to true (nonzero), then the socket is re‐
stricted to sending and receiving IPv6 packets only.  In this
case, an IPv4 and an IPv6 application can bind to a single
port at the same time.

>If this flag is set to false (zero), then the socket can be
used to send and receive packets to and from an IPv6 address
or an IPv4-mapped IPv6 address.

>The argument is a pointer to a boolean value in an integer.

>The default value for this flag is defined by the contents of
the file /proc/sys/net/ipv6/bindv6only.  The default value for
that file is 0 (false).

在檔案的 `mosquitto/src/net.c` 第 75 行左右加入

```c
#ifndef IPV6_V6ONLY
#define IPV6_V6ONLY 1
#endif
```

大功告成啦~ Hooooooray~

快快樂樂享受 Mosquitto 吧~ 我們下次見~

後記：其實過程當中一度編譯成功但是 mosquitto\_sub, mosquitto\_pub 這兩隻一直不能正常運作，mosquitto 可以動(但是 bridge mode 不行)，還想說到底是哪裡有病阿...結果原來是我網路上抄來的 `AI_ADDRCONFIG` 這個數值害慘了...因為有些定義成 `0x0020` 我直接拿來用顯然是錯誤的 `0x0000` 在我們的 case 中才是正解，所以鄉親啊！！！要注意。