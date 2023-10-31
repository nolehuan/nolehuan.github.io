---
title: ptp
date: 2023-11-29 21:26:29
abstracts: ptp
tags:
  - time
categories:
  - tool
cover: ''
feature: false
mathjax: false
---


#### 时钟

* UNIX 从1970年1月1日开始所经过的秒数

* CLOCK_REALTIME 系统实时时间，UNIX 时间

* CLOCK_MONOTONIC 系统单调时间，系统启动后经过的秒数

#### 查看硬件支持

`ifconfig` 查看网卡

```shell
$ ifconfig
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 1500  bytes 180446 (180.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1500  bytes 180446 (180.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp0s20f3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.99.101  netmask 255.255.255.0  broadcast 192.168.99.255
        inet6 fe80::f40b:3351:553f:66ba  prefixlen 64  scopeid 0x20<link>
        ether 08:71:90:3c:dc:05  txqueuelen 1000  (Ethernet)
        RX packets 34174  bytes 42219505 (42.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 16487  bytes 2915006 (2.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

`ethtool -T wlp0s20f3` 查看网卡是否支持 PTP 同步

```shell
$ ethtool -T wlp0s20f3
Time stamping parameters for wlp0s20f3:
Capabilities:
	software-receive      (SOF_TIMESTAMPING_RX_SOFTWARE)
	software-system-clock (SOF_TIMESTAMPING_SOFTWARE)
PTP Hardware Clock: none
Hardware Transmit Timestamp Modes: none
Hardware Receive Filter Modes: none
```
PTP Hardware Clock: none 网卡不支持 PTP 同步

#### PTP 同步

开始同步前建议关闭 WIFI

* 主钟

`sudo ptp4l -i eno1 -m -H` 
`-i` 网卡设备
`-m` 将消息打印到标准输出
`-H` 硬件时间戳

`sudo phc2sys -c eno1 -s CLOCK_REALTIME -w -m` 将系统时钟同步到网卡的 PTP 硬件时钟
`-c` 从时钟，默认 CLOCK_REALTIME
`-s` 主时钟
`-w` 等待 ptp4l
`-m` 将消息打印到标准输出

* 从钟

`sudo ptp4l -i eno1 -m -H -s`
`-s` 从时钟模式

`sudo phc2sys -s eno1 -w -m` 将网卡的 PTP 硬件时钟同步到系统时钟

