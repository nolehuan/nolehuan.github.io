---
title: asm
date: 2023-11-27 21:30:29
abstracts: lea, mov
tags:
  - asm
categories:
  - coding
cover: ''
feature: false
mathjax: false
---

### lea 与 mov 的区别

* `lea` load effective address

可以用来将一个内存地址直接赋给目的操作数，如 `lea eax, [ebx+8]` 将值 `ebx+8` 直接赋给 `eax`，而不是把内存地址 `ebx+8` 处的数据赋给 `eax`

* `mov`

`mov eax, [ebx+8]` 将内存地址 `ebx+8` 处的数据赋给 `eax`

### 参考

https://www.zhihu.com/question/40720890/answer/110774673
