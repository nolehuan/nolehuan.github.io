---
title: lttng
date: 2023-12-09 13:11:29
abstracts: performance
tags:
  - trace
categories:
  - linux
cover: ''
feature: false
mathjax: false
---


#### install

```shell
$ sudo apt-add-repository ppa:lttng/stable-2.13
$ sudo apt-get update
$ sudo apt-get install lttng-tools
$ sudo apt-get install lttng-modules-dkms
$ sudo apt-get install liblttng-ust-dev
$ sudo apt-get install liblttng-ctl-dev
$ sudo apt-get install babeltrace
```


#### usage

```c++

```

```shell
$ g++ -o test_lttng test_lttng.cc -llttng-ust -llttng-ctl
```


#### 参考

https://lttng.org/
https://github.com/lttng/
