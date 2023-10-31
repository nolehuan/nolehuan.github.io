---
title: elf
date: 2023-12-23 17:40:29
abstracts: layout
tags:
  - cpp
categories:
  - linux
cover: ''
feature: false
mathjax: false
---

#### demo

```c
int printf(const char* format, ...);

int global_init_var = 84;
int global_uninit_var;

void func(int i) {
  printf("%d\n", i);
}

int main(void) {
  static int static_var = 85;
  static int static_uninit_var;

  int a = 1;
  int b;

  func(static_var + static_uninit_var + a + b);

  return a;
}
```

```shell
$ gcc -c SimpleSection.c
$ objdump -h SimpleSection.o 

SimpleSection.o:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000057  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, CODE
  1 .data         00000008  0000000000000000  0000000000000000  00000098  2**2
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000004  0000000000000000  0000000000000000  000000a0  2**2
                  ALLOC
  3 .rodata       00000004  0000000000000000  0000000000000000  000000a0  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .comment      0000002a  0000000000000000  0000000000000000  000000a4  2**0
                  CONTENTS, READONLY
  5 .note.GNU-stack 00000000  0000000000000000  0000000000000000  000000ce  2**0
                  CONTENTS, READONLY
  6 .eh_frame     00000058  0000000000000000  0000000000000000  000000d0  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
$ size SimpleSection.o 
   text	   data	    bss	    dec	    hex	filename
    179	      8	      4	    191	     bf	SimpleSection.o
```

<div align="center">
  <img src="/img/elf.png">
</div>

`.text` 代码段
`.data` 数据段，保存已初始化的全局变量global_init_var和局部静态变量static_var
`.bss` BSS段，为未初始化的全局变量global_uninit_var和局部静态变量static_uninit_var预留空间，不存储变量
`.rodata` 只读数据段，保存字符串常量"%d\n"和只读变量const
`.comment` 注释信息段
`.note.GNU-stack` 堆栈提示段
`CONTENTS` 表示在文件中存在
`Size` 段的长度
`File off` 段的位置

```shell
$ objdump -s -d SimpleSection.o 

SimpleSection.o:     file format elf64-x86-64

Contents of section .text:
 0000 554889e5 4883ec10 897dfc8b 45fc89c6  UH..H....}..E...
 0010 488d3d00 000000b8 00000000 e8000000  H.=.............
 0020 0090c9c3 554889e5 4883ec10 c745f801  ....UH..H....E..
 0030 0000008b 15000000 008b0500 00000001  ................
 0040 c28b45f8 01c28b45 fc01d089 c7e80000  ..E....E........
 0050 00008b45 f8c9c3                      ...E...         
Contents of section .data:
 0000 54000000 55000000                    T...U...        
Contents of section .rodata:
 0000 25640a00                             %d..            
Contents of section .comment:
 0000 00474343 3a202855 62756e74 7520372e  .GCC: (Ubuntu 7.
 0010 352e302d 33756275 6e747531 7e31382e  5.0-3ubuntu1~18.
 0020 30342920 372e352e 3000               04) 7.5.0.      
Contents of section .eh_frame:
 0000 14000000 00000000 017a5200 01781001  .........zR..x..
 0010 1b0c0708 90010000 1c000000 1c000000  ................
 0020 00000000 24000000 00410e10 8602430d  ....$....A....C.
 0030 065f0c07 08000000 1c000000 3c000000  ._..........<...
 0040 00000000 33000000 00410e10 8602430d  ....3....A....C.
 0050 066e0c07 08000000                    .n......        

Disassembly of section .text:

0000000000000000 <func>:
   0:	55                   	push   %rbp
   1:	48 89 e5             	mov    %rsp,%rbp
   4:	48 83 ec 10          	sub    $0x10,%rsp
   8:	89 7d fc             	mov    %edi,-0x4(%rbp)
   b:	8b 45 fc             	mov    -0x4(%rbp),%eax
   e:	89 c6                	mov    %eax,%esi
  10:	48 8d 3d 00 00 00 00 	lea    0x0(%rip),%rdi        # 17 <func+0x17>
  17:	b8 00 00 00 00       	mov    $0x0,%eax
  1c:	e8 00 00 00 00       	callq  21 <func+0x21>
  21:	90                   	nop
  22:	c9                   	leaveq 
  23:	c3                   	retq   

0000000000000024 <main>:
  24:	55                   	push   %rbp
  25:	48 89 e5             	mov    %rsp,%rbp
  28:	48 83 ec 10          	sub    $0x10,%rsp
  2c:	c7 45 f8 01 00 00 00 	movl   $0x1,-0x8(%rbp)
  33:	8b 15 00 00 00 00    	mov    0x0(%rip),%edx        # 39 <main+0x15>
  39:	8b 05 00 00 00 00    	mov    0x0(%rip),%eax        # 3f <main+0x1b>
  3f:	01 c2                	add    %eax,%edx
  41:	8b 45 f8             	mov    -0x8(%rbp),%eax
  44:	01 c2                	add    %eax,%edx
  46:	8b 45 fc             	mov    -0x4(%rbp),%eax
  49:	01 d0                	add    %edx,%eax
  4b:	89 c7                	mov    %eax,%edi
  4d:	e8 00 00 00 00       	callq  52 <main+0x2e>
  52:	8b 45 f8             	mov    -0x8(%rbp),%eax
  55:	c9                   	leaveq 
  56:	c3                   	retq
```

`-s` 以十六进制打印所有段的内容
`-d` 将所有包含指令的段反汇编
`-x` 显示所有头信息


```shell
$ objdump -x -s -d SimpleSection.o
...
Contents of section .data:
 0000 54000000 55000000                    T...U...        
Contents of section .rodata:
 0000 25640a00                             %d..
...
```

`.data` 段的前四个字节54000000（小端存储）即global_init_var=84




