---
title: freopen
date: 2023-12-30 12:09:29
abstracts: redirect
tags:
  - c
categories:
  - linux
cover: ''
feature: false
mathjax: false
---


拒绝三方库日志霸屏


```c++
#include <stdio.h>

int main()
{
  freopen("log.txt", "w", stdout);
  freopen("error.txt", "w", stderr);
  printf("out\n");
  perror("error");
  freopen("/dev/tty", "w", stdout);
  freopen("/dev/tty", "w", stderr);
  printf("out\n");
  perror("error");
  return 0;
}
```

```shell
$ g++ -o log log.cc 
$ ./log 
out
error: Invalid argument
$ cat log.txt 
out
$ cat error.txt 
error: Success
```

`perror` 将上一个函数发生错误的原因输出到标准设备`stderr`，打印字符串后加`errno`对应的错误原因字符串，无错误则打印`Success`
