---
title: strip
date: 2023-11-30 21:20:29
abstracts: 动态库裁剪
tags:
  - shell
  - so
categories:
  - script
cover: ''
feature: false
mathjax: false
---

### 动态库裁剪

#### strip

* `-s` `--strip-all` 删除所有符号和重定位信息，.dynsym 和 .dynstr 表信息依然存在，用于动态链接库链接使用


<details>
<summary>details</summary>

```shell
$ ll | grep libtest.so
-rwxrwxr-x 1 nolehuan nolehuan 10480 11月 26 18:32 libtest.so*
$ readelf -s libtest.so

Symbol table '.dynsym' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)
     6: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
     7: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
     8: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
     9: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    10: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init
    11: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    12: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func

Symbol table '.symtab' contains 59 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 00000000000001c8     0 SECTION LOCAL  DEFAULT    1 
     2: 00000000000001f0     0 SECTION LOCAL  DEFAULT    2 
     3: 0000000000000230     0 SECTION LOCAL  DEFAULT    3 
     4: 0000000000000368     0 SECTION LOCAL  DEFAULT    4 
     5: 0000000000000406     0 SECTION LOCAL  DEFAULT    5 
     6: 0000000000000420     0 SECTION LOCAL  DEFAULT    6 
     7: 0000000000000440     0 SECTION LOCAL  DEFAULT    7 
     8: 00000000000004e8     0 SECTION LOCAL  DEFAULT    8 
     9: 0000000000000500     0 SECTION LOCAL  DEFAULT    9 
    10: 0000000000000520     0 SECTION LOCAL  DEFAULT   10 
    11: 0000000000000540     0 SECTION LOCAL  DEFAULT   11 
    12: 0000000000000550     0 SECTION LOCAL  DEFAULT   12 
    13: 0000000000000660     0 SECTION LOCAL  DEFAULT   13 
    14: 0000000000000669     0 SECTION LOCAL  DEFAULT   14 
    15: 0000000000000674     0 SECTION LOCAL  DEFAULT   15 
    16: 00000000000006a0     0 SECTION LOCAL  DEFAULT   16 
    17: 0000000000200e10     0 SECTION LOCAL  DEFAULT   17 
    18: 0000000000200e18     0 SECTION LOCAL  DEFAULT   18 
    19: 0000000000200e20     0 SECTION LOCAL  DEFAULT   19 
    20: 0000000000200fe0     0 SECTION LOCAL  DEFAULT   20 
    21: 0000000000201000     0 SECTION LOCAL  DEFAULT   21 
    22: 0000000000201020     0 SECTION LOCAL  DEFAULT   22 
    23: 0000000000201028     0 SECTION LOCAL  DEFAULT   23 
    24: 0000000000000000     0 SECTION LOCAL  DEFAULT   24 
    25: 0000000000000000     0 SECTION LOCAL  DEFAULT   25 
    26: 0000000000000000     0 SECTION LOCAL  DEFAULT   26 
    27: 0000000000000000     0 SECTION LOCAL  DEFAULT   27 
    28: 0000000000000000     0 SECTION LOCAL  DEFAULT   28 
    29: 0000000000000000     0 SECTION LOCAL  DEFAULT   29 
    30: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    31: 0000000000000550     0 FUNC    LOCAL  DEFAULT   12 deregister_tm_clones
    32: 0000000000000590     0 FUNC    LOCAL  DEFAULT   12 register_tm_clones
    33: 00000000000005e0     0 FUNC    LOCAL  DEFAULT   12 __do_global_dtors_aux
    34: 0000000000201028     1 OBJECT  LOCAL  DEFAULT   23 completed.7698
    35: 0000000000200e18     0 OBJECT  LOCAL  DEFAULT   18 __do_global_dtors_aux_fin
    36: 0000000000000620     0 FUNC    LOCAL  DEFAULT   12 frame_dummy
    37: 0000000000200e10     0 OBJECT  LOCAL  DEFAULT   17 __frame_dummy_init_array_
    38: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS libtest.c
    39: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    40: 0000000000000738     0 OBJECT  LOCAL  DEFAULT   16 __FRAME_END__
    41: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS 
    42: 0000000000201020     0 OBJECT  LOCAL  DEFAULT   22 __dso_handle
    43: 0000000000200e20     0 OBJECT  LOCAL  DEFAULT   19 _DYNAMIC
    44: 0000000000000674     0 NOTYPE  LOCAL  DEFAULT   15 __GNU_EH_FRAME_HDR
    45: 0000000000201028     0 OBJECT  LOCAL  DEFAULT   22 __TMC_END__
    46: 0000000000201000     0 OBJECT  LOCAL  DEFAULT   21 _GLOBAL_OFFSET_TABLE_
    47: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
    48: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
    49: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@@GLIBC_2.2.5
    50: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
    51: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    52: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    53: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func
    54: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
    55: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    56: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    57: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@@GLIBC_2.2
    58: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init

$ strip -s libtest.so
$ readelf -s libtest.so 

Symbol table '.dynsym' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)
     6: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
     7: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
     8: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
     9: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    10: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init
    11: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    12: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func

$ ll | grep libtest.so
-rwxrwxr-x 1 nolehuan nolehuan  6072 11月 30 21:40 libtest.so*
```
</details>

`file` 命令查看文件类型、架构、是否 stripped 等信息
```shell
$ file libtest.so 
libtest.so.bk: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, BuildID[sha1]=3a6987a7f71f453637ab35daa89ec141fba9026b, with debug_info, not stripped
$ strip -s libtest.so
$ file libtest.so
libtest.so: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, BuildID[sha1]=3a6987a7f71f453637ab35daa89ec141fba9026b, stripped
```

* `-g` `-S` `-d` `--strip-debug` 删除所有调试符号和段

```shell
$ ll | grep libtest.so
-rwxrwxr-x 1 nolehuan nolehuan 10480 11月 30 22:02 libtest.so*
$ strip --strip-debug libtest.so
$ ll | grep libtest.so
-rwxrwxr-x 1 nolehuan nolehuan  7808 11月 30 22:03 libtest.so*
```
Section Headers 与 Symbol table 中的以下信息被删除
```shell
...
  [25] .debug_aranges    PROGBITS         0000000000000000  00001051
       0000000000000030  0000000000000000           0     0     1
  [26] .debug_info       PROGBITS         0000000000000000  00001081
       000000000000035b  0000000000000000           0     0     1
  [27] .debug_abbrev     PROGBITS         0000000000000000  000013dc
       0000000000000109  0000000000000000           0     0     1
  [28] .debug_line       PROGBITS         0000000000000000  000014e5
       00000000000000d7  0000000000000000           0     0     1
  [29] .debug_str        PROGBITS         0000000000000000  000015bc
       0000000000000296  0000000000000001  MS       0     0     1
...
    30: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
...
    38: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS libtest.c
    39: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
...
```

<details>
<summary>details</summary>

```shell
$ readelf -a libtest.so
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x550
  Start of program headers:          64 (bytes into file)
  Start of section headers:          8368 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         7
  Size of section headers:           64 (bytes)
  Number of section headers:         33
  Section header string table index: 32

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .note.gnu.build-i NOTE             00000000000001c8  000001c8
       0000000000000024  0000000000000000   A       0     0     4
  [ 2] .gnu.hash         GNU_HASH         00000000000001f0  000001f0
       0000000000000040  0000000000000000   A       3     0     8
  [ 3] .dynsym           DYNSYM           0000000000000230  00000230
       0000000000000138  0000000000000018   A       4     1     8
  [ 4] .dynstr           STRTAB           0000000000000368  00000368
       000000000000009d  0000000000000000   A       0     0     1
  [ 5] .gnu.version      VERSYM           0000000000000406  00000406
       000000000000001a  0000000000000002   A       3     0     2
  [ 6] .gnu.version_r    VERNEED          0000000000000420  00000420
       0000000000000020  0000000000000000   A       4     1     8
  [ 7] .rela.dyn         RELA             0000000000000440  00000440
       00000000000000a8  0000000000000018   A       3     0     8
  [ 8] .rela.plt         RELA             00000000000004e8  000004e8
       0000000000000018  0000000000000018  AI       3    21     8
  [ 9] .init             PROGBITS         0000000000000500  00000500
       0000000000000017  0000000000000000  AX       0     0     4
  [10] .plt              PROGBITS         0000000000000520  00000520
       0000000000000020  0000000000000010  AX       0     0     16
  [11] .plt.got          PROGBITS         0000000000000540  00000540
       0000000000000008  0000000000000008  AX       0     0     8
  [12] .text             PROGBITS         0000000000000550  00000550
       000000000000010d  0000000000000000  AX       0     0     16
  [13] .fini             PROGBITS         0000000000000660  00000660
       0000000000000009  0000000000000000  AX       0     0     4
  [14] .rodata           PROGBITS         0000000000000669  00000669
       0000000000000008  0000000000000000   A       0     0     1
  [15] .eh_frame_hdr     PROGBITS         0000000000000674  00000674
       000000000000002c  0000000000000000   A       0     0     4
  [16] .eh_frame         PROGBITS         00000000000006a0  000006a0
       000000000000009c  0000000000000000   A       0     0     8
  [17] .init_array       INIT_ARRAY       0000000000200e10  00000e10
       0000000000000008  0000000000000008  WA       0     0     8
  [18] .fini_array       FINI_ARRAY       0000000000200e18  00000e18
       0000000000000008  0000000000000008  WA       0     0     8
  [19] .dynamic          DYNAMIC          0000000000200e20  00000e20
       00000000000001c0  0000000000000010  WA       4     0     8
  [20] .got              PROGBITS         0000000000200fe0  00000fe0
       0000000000000020  0000000000000008  WA       0     0     8
  [21] .got.plt          PROGBITS         0000000000201000  00001000
       0000000000000020  0000000000000008  WA       0     0     8
  [22] .data             PROGBITS         0000000000201020  00001020
       0000000000000008  0000000000000000  WA       0     0     8
  [23] .bss              NOBITS           0000000000201028  00001028
       0000000000000008  0000000000000000  WA       0     0     1
  [24] .comment          PROGBITS         0000000000000000  00001028
       0000000000000029  0000000000000001  MS       0     0     1
  [25] .debug_aranges    PROGBITS         0000000000000000  00001051
       0000000000000030  0000000000000000           0     0     1
  [26] .debug_info       PROGBITS         0000000000000000  00001081
       000000000000035b  0000000000000000           0     0     1
  [27] .debug_abbrev     PROGBITS         0000000000000000  000013dc
       0000000000000109  0000000000000000           0     0     1
  [28] .debug_line       PROGBITS         0000000000000000  000014e5
       00000000000000d7  0000000000000000           0     0     1
  [29] .debug_str        PROGBITS         0000000000000000  000015bc
       0000000000000296  0000000000000001  MS       0     0     1
  [30] .symtab           SYMTAB           0000000000000000  00001858
       0000000000000588  0000000000000018          31    47     8
  [31] .strtab           STRTAB           0000000000000000  00001de0
       000000000000019b  0000000000000000           0     0     1
  [32] .shstrtab         STRTAB           0000000000000000  00001f7b
       0000000000000131  0000000000000000           0     0     1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  l (large), p (processor specific)

There are no section groups in this file.

Program Headers:
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  LOAD           0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x000000000000073c 0x000000000000073c  R E    0x200000
  LOAD           0x0000000000000e10 0x0000000000200e10 0x0000000000200e10
                 0x0000000000000218 0x0000000000000220  RW     0x200000
  DYNAMIC        0x0000000000000e20 0x0000000000200e20 0x0000000000200e20
                 0x00000000000001c0 0x00000000000001c0  RW     0x8
  NOTE           0x00000000000001c8 0x00000000000001c8 0x00000000000001c8
                 0x0000000000000024 0x0000000000000024  R      0x4
  GNU_EH_FRAME   0x0000000000000674 0x0000000000000674 0x0000000000000674
                 0x000000000000002c 0x000000000000002c  R      0x4
  GNU_STACK      0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x0000000000000000 0x0000000000000000  RW     0x10
  GNU_RELRO      0x0000000000000e10 0x0000000000200e10 0x0000000000200e10
                 0x00000000000001f0 0x00000000000001f0  R      0x1

 Section to Segment mapping:
  Segment Sections...
   00     .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .plt.got .text .fini .rodata .eh_frame_hdr .eh_frame 
   01     .init_array .fini_array .dynamic .got .got.plt .data .bss 
   02     .dynamic 
   03     .note.gnu.build-id 
   04     .eh_frame_hdr 
   05     
   06     .init_array .fini_array .dynamic .got 

Dynamic section at offset 0xe20 contains 24 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000c (INIT)               0x500
 0x000000000000000d (FINI)               0x660
 0x0000000000000019 (INIT_ARRAY)         0x200e10
 0x000000000000001b (INIT_ARRAYSZ)       8 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x200e18
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x000000006ffffef5 (GNU_HASH)           0x1f0
 0x0000000000000005 (STRTAB)             0x368
 0x0000000000000006 (SYMTAB)             0x230
 0x000000000000000a (STRSZ)              157 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000003 (PLTGOT)             0x201000
 0x0000000000000002 (PLTRELSZ)           24 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x4e8
 0x0000000000000007 (RELA)               0x440
 0x0000000000000008 (RELASZ)             168 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x420
 0x000000006fffffff (VERNEEDNUM)         1
 0x000000006ffffff0 (VERSYM)             0x406
 0x000000006ffffff9 (RELACOUNT)          3
 0x0000000000000000 (NULL)               0x0

Relocation section '.rela.dyn' at offset 0x440 contains 7 entries:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000200e10  000000000008 R_X86_64_RELATIVE                    620
000000200e18  000000000008 R_X86_64_RELATIVE                    5e0
000000201020  000000000008 R_X86_64_RELATIVE                    201020
000000200fe0  000100000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_deregisterTMClone + 0
000000200fe8  000300000006 R_X86_64_GLOB_DAT 0000000000000000 __gmon_start__ + 0
000000200ff0  000400000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_registerTMCloneTa + 0
000000200ff8  000500000006 R_X86_64_GLOB_DAT 0000000000000000 __cxa_finalize@GLIBC_2.2.5 + 0

Relocation section '.rela.plt' at offset 0x4e8 contains 1 entry:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000201018  000200000007 R_X86_64_JUMP_SLO 0000000000000000 puts@GLIBC_2.2.5 + 0

The decoding of unwind sections for machine type Advanced Micro Devices X86-64 is not currently supported.

Symbol table '.dynsym' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)
     6: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
     7: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
     8: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
     9: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    10: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init
    11: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    12: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func

Symbol table '.symtab' contains 59 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 00000000000001c8     0 SECTION LOCAL  DEFAULT    1 
     2: 00000000000001f0     0 SECTION LOCAL  DEFAULT    2 
     3: 0000000000000230     0 SECTION LOCAL  DEFAULT    3 
     4: 0000000000000368     0 SECTION LOCAL  DEFAULT    4 
     5: 0000000000000406     0 SECTION LOCAL  DEFAULT    5 
     6: 0000000000000420     0 SECTION LOCAL  DEFAULT    6 
     7: 0000000000000440     0 SECTION LOCAL  DEFAULT    7 
     8: 00000000000004e8     0 SECTION LOCAL  DEFAULT    8 
     9: 0000000000000500     0 SECTION LOCAL  DEFAULT    9 
    10: 0000000000000520     0 SECTION LOCAL  DEFAULT   10 
    11: 0000000000000540     0 SECTION LOCAL  DEFAULT   11 
    12: 0000000000000550     0 SECTION LOCAL  DEFAULT   12 
    13: 0000000000000660     0 SECTION LOCAL  DEFAULT   13 
    14: 0000000000000669     0 SECTION LOCAL  DEFAULT   14 
    15: 0000000000000674     0 SECTION LOCAL  DEFAULT   15 
    16: 00000000000006a0     0 SECTION LOCAL  DEFAULT   16 
    17: 0000000000200e10     0 SECTION LOCAL  DEFAULT   17 
    18: 0000000000200e18     0 SECTION LOCAL  DEFAULT   18 
    19: 0000000000200e20     0 SECTION LOCAL  DEFAULT   19 
    20: 0000000000200fe0     0 SECTION LOCAL  DEFAULT   20 
    21: 0000000000201000     0 SECTION LOCAL  DEFAULT   21 
    22: 0000000000201020     0 SECTION LOCAL  DEFAULT   22 
    23: 0000000000201028     0 SECTION LOCAL  DEFAULT   23 
    24: 0000000000000000     0 SECTION LOCAL  DEFAULT   24 
    25: 0000000000000000     0 SECTION LOCAL  DEFAULT   25 
    26: 0000000000000000     0 SECTION LOCAL  DEFAULT   26 
    27: 0000000000000000     0 SECTION LOCAL  DEFAULT   27 
    28: 0000000000000000     0 SECTION LOCAL  DEFAULT   28 
    29: 0000000000000000     0 SECTION LOCAL  DEFAULT   29 
    30: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    31: 0000000000000550     0 FUNC    LOCAL  DEFAULT   12 deregister_tm_clones
    32: 0000000000000590     0 FUNC    LOCAL  DEFAULT   12 register_tm_clones
    33: 00000000000005e0     0 FUNC    LOCAL  DEFAULT   12 __do_global_dtors_aux
    34: 0000000000201028     1 OBJECT  LOCAL  DEFAULT   23 completed.7698
    35: 0000000000200e18     0 OBJECT  LOCAL  DEFAULT   18 __do_global_dtors_aux_fin
    36: 0000000000000620     0 FUNC    LOCAL  DEFAULT   12 frame_dummy
    37: 0000000000200e10     0 OBJECT  LOCAL  DEFAULT   17 __frame_dummy_init_array_
    38: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS libtest.c
    39: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    40: 0000000000000738     0 OBJECT  LOCAL  DEFAULT   16 __FRAME_END__
    41: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS 
    42: 0000000000201020     0 OBJECT  LOCAL  DEFAULT   22 __dso_handle
    43: 0000000000200e20     0 OBJECT  LOCAL  DEFAULT   19 _DYNAMIC
    44: 0000000000000674     0 NOTYPE  LOCAL  DEFAULT   15 __GNU_EH_FRAME_HDR
    45: 0000000000201028     0 OBJECT  LOCAL  DEFAULT   22 __TMC_END__
    46: 0000000000201000     0 OBJECT  LOCAL  DEFAULT   21 _GLOBAL_OFFSET_TABLE_
    47: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
    48: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
    49: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@@GLIBC_2.2.5
    50: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
    51: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    52: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    53: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func
    54: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
    55: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    56: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    57: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@@GLIBC_2.2
    58: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init

Histogram for `.gnu.hash' bucket list length (total of 3 buckets):
 Length  Number     % of total  Coverage
      0  0          (  0.0%)
      1  0          (  0.0%)      0.0%
      2  2          ( 66.7%)     57.1%
      3  1          ( 33.3%)    100.0%

Version symbols section '.gnu.version' contains 13 entries:
 Addr: 0000000000000406  Offset: 0x000406  Link: 3 (.dynsym)
  000:   0 (*local*)       0 (*local*)       2 (GLIBC_2.2.5)   0 (*local*)    
  004:   0 (*local*)       2 (GLIBC_2.2.5)   1 (*global*)      1 (*global*)   
  008:   1 (*global*)      1 (*global*)      1 (*global*)      1 (*global*)   
  00c:   1 (*global*)   

Version needs section '.gnu.version_r' contains 1 entry:
 Addr: 0x0000000000000420  Offset: 0x000420  Link: 4 (.dynstr)
  000000: Version: 1  File: libc.so.6  Cnt: 1
  0x0010:   Name: GLIBC_2.2.5  Flags: none  Version: 2

Displaying notes found in: .note.gnu.build-id
  Owner                 Data size	Description
  GNU                  0x00000014	NT_GNU_BUILD_ID (unique build ID bitstring)
    Build ID: 3a6987a7f71f453637ab35daa89ec141fba9026b

$ strip --strip-debug libtest.so
$ readelf -a libtest.so
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x550
  Start of program headers:          64 (bytes into file)
  Start of section headers:          6016 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         7
  Size of section headers:           64 (bytes)
  Number of section headers:         28
  Section header string table index: 27

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .note.gnu.build-i NOTE             00000000000001c8  000001c8
       0000000000000024  0000000000000000   A       0     0     4
  [ 2] .gnu.hash         GNU_HASH         00000000000001f0  000001f0
       0000000000000040  0000000000000000   A       3     0     8
  [ 3] .dynsym           DYNSYM           0000000000000230  00000230
       0000000000000138  0000000000000018   A       4     1     8
  [ 4] .dynstr           STRTAB           0000000000000368  00000368
       000000000000009d  0000000000000000   A       0     0     1
  [ 5] .gnu.version      VERSYM           0000000000000406  00000406
       000000000000001a  0000000000000002   A       3     0     2
  [ 6] .gnu.version_r    VERNEED          0000000000000420  00000420
       0000000000000020  0000000000000000   A       4     1     8
  [ 7] .rela.dyn         RELA             0000000000000440  00000440
       00000000000000a8  0000000000000018   A       3     0     8
  [ 8] .rela.plt         RELA             00000000000004e8  000004e8
       0000000000000018  0000000000000018  AI       3    21     8
  [ 9] .init             PROGBITS         0000000000000500  00000500
       0000000000000017  0000000000000000  AX       0     0     4
  [10] .plt              PROGBITS         0000000000000520  00000520
       0000000000000020  0000000000000010  AX       0     0     16
  [11] .plt.got          PROGBITS         0000000000000540  00000540
       0000000000000008  0000000000000008  AX       0     0     8
  [12] .text             PROGBITS         0000000000000550  00000550
       000000000000010d  0000000000000000  AX       0     0     16
  [13] .fini             PROGBITS         0000000000000660  00000660
       0000000000000009  0000000000000000  AX       0     0     4
  [14] .rodata           PROGBITS         0000000000000669  00000669
       0000000000000008  0000000000000000   A       0     0     1
  [15] .eh_frame_hdr     PROGBITS         0000000000000674  00000674
       000000000000002c  0000000000000000   A       0     0     4
  [16] .eh_frame         PROGBITS         00000000000006a0  000006a0
       000000000000009c  0000000000000000   A       0     0     8
  [17] .init_array       INIT_ARRAY       0000000000200e10  00000e10
       0000000000000008  0000000000000008  WA       0     0     8
  [18] .fini_array       FINI_ARRAY       0000000000200e18  00000e18
       0000000000000008  0000000000000008  WA       0     0     8
  [19] .dynamic          DYNAMIC          0000000000200e20  00000e20
       00000000000001c0  0000000000000010  WA       4     0     8
  [20] .got              PROGBITS         0000000000200fe0  00000fe0
       0000000000000020  0000000000000008  WA       0     0     8
  [21] .got.plt          PROGBITS         0000000000201000  00001000
       0000000000000020  0000000000000008  WA       0     0     8
  [22] .data             PROGBITS         0000000000201020  00001020
       0000000000000008  0000000000000000  WA       0     0     8
  [23] .bss              NOBITS           0000000000201028  00001028
       0000000000000008  0000000000000000  WA       0     0     1
  [24] .comment          PROGBITS         0000000000000000  00001028
       0000000000000029  0000000000000001  MS       0     0     1
  [25] .symtab           SYMTAB           0000000000000000  00001058
       00000000000004b0  0000000000000018          26    38     8
  [26] .strtab           STRTAB           0000000000000000  00001508
       0000000000000186  0000000000000000           0     0     1
  [27] .shstrtab         STRTAB           0000000000000000  0000168e
       00000000000000f1  0000000000000000           0     0     1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  l (large), p (processor specific)

There are no section groups in this file.

Program Headers:
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  LOAD           0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x000000000000073c 0x000000000000073c  R E    0x200000
  LOAD           0x0000000000000e10 0x0000000000200e10 0x0000000000200e10
                 0x0000000000000218 0x0000000000000220  RW     0x200000
  DYNAMIC        0x0000000000000e20 0x0000000000200e20 0x0000000000200e20
                 0x00000000000001c0 0x00000000000001c0  RW     0x8
  NOTE           0x00000000000001c8 0x00000000000001c8 0x00000000000001c8
                 0x0000000000000024 0x0000000000000024  R      0x4
  GNU_EH_FRAME   0x0000000000000674 0x0000000000000674 0x0000000000000674
                 0x000000000000002c 0x000000000000002c  R      0x4
  GNU_STACK      0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x0000000000000000 0x0000000000000000  RW     0x10
  GNU_RELRO      0x0000000000000e10 0x0000000000200e10 0x0000000000200e10
                 0x00000000000001f0 0x00000000000001f0  R      0x1

 Section to Segment mapping:
  Segment Sections...
   00     .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .plt.got .text .fini .rodata .eh_frame_hdr .eh_frame 
   01     .init_array .fini_array .dynamic .got .got.plt .data .bss 
   02     .dynamic 
   03     .note.gnu.build-id 
   04     .eh_frame_hdr 
   05     
   06     .init_array .fini_array .dynamic .got 

Dynamic section at offset 0xe20 contains 24 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000c (INIT)               0x500
 0x000000000000000d (FINI)               0x660
 0x0000000000000019 (INIT_ARRAY)         0x200e10
 0x000000000000001b (INIT_ARRAYSZ)       8 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x200e18
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x000000006ffffef5 (GNU_HASH)           0x1f0
 0x0000000000000005 (STRTAB)             0x368
 0x0000000000000006 (SYMTAB)             0x230
 0x000000000000000a (STRSZ)              157 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000003 (PLTGOT)             0x201000
 0x0000000000000002 (PLTRELSZ)           24 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x4e8
 0x0000000000000007 (RELA)               0x440
 0x0000000000000008 (RELASZ)             168 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x420
 0x000000006fffffff (VERNEEDNUM)         1
 0x000000006ffffff0 (VERSYM)             0x406
 0x000000006ffffff9 (RELACOUNT)          3
 0x0000000000000000 (NULL)               0x0

Relocation section '.rela.dyn' at offset 0x440 contains 7 entries:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000200e10  000000000008 R_X86_64_RELATIVE                    620
000000200e18  000000000008 R_X86_64_RELATIVE                    5e0
000000201020  000000000008 R_X86_64_RELATIVE                    201020
000000200fe0  000100000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_deregisterTMClone + 0
000000200fe8  000300000006 R_X86_64_GLOB_DAT 0000000000000000 __gmon_start__ + 0
000000200ff0  000400000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_registerTMCloneTa + 0
000000200ff8  000500000006 R_X86_64_GLOB_DAT 0000000000000000 __cxa_finalize@GLIBC_2.2.5 + 0

Relocation section '.rela.plt' at offset 0x4e8 contains 1 entry:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000201018  000200000007 R_X86_64_JUMP_SLO 0000000000000000 puts@GLIBC_2.2.5 + 0

The decoding of unwind sections for machine type Advanced Micro Devices X86-64 is not currently supported.

Symbol table '.dynsym' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)
     6: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
     7: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
     8: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
     9: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    10: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init
    11: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    12: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func

Symbol table '.symtab' contains 50 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000550     0 FUNC    LOCAL  DEFAULT   12 deregister_tm_clones
     2: 0000000000000590     0 FUNC    LOCAL  DEFAULT   12 register_tm_clones
     3: 00000000000005e0     0 FUNC    LOCAL  DEFAULT   12 __do_global_dtors_aux
     4: 0000000000201028     1 OBJECT  LOCAL  DEFAULT   23 completed.7698
     5: 0000000000200e18     0 OBJECT  LOCAL  DEFAULT   18 __do_global_dtors_aux_fin
     6: 0000000000000620     0 FUNC    LOCAL  DEFAULT   12 frame_dummy
     7: 0000000000200e10     0 OBJECT  LOCAL  DEFAULT   17 __frame_dummy_init_array_
     8: 0000000000000738     0 OBJECT  LOCAL  DEFAULT   16 __FRAME_END__
     9: 0000000000201020     0 OBJECT  LOCAL  DEFAULT   22 __dso_handle
    10: 0000000000200e20     0 OBJECT  LOCAL  DEFAULT   19 _DYNAMIC
    11: 0000000000000674     0 NOTYPE  LOCAL  DEFAULT   15 __GNU_EH_FRAME_HDR
    12: 0000000000201028     0 OBJECT  LOCAL  DEFAULT   22 __TMC_END__
    13: 0000000000201000     0 OBJECT  LOCAL  DEFAULT   21 _GLOBAL_OFFSET_TABLE_
    14: 00000000000001c8     0 SECTION LOCAL  DEFAULT    1 
    15: 00000000000001f0     0 SECTION LOCAL  DEFAULT    2 
    16: 0000000000000230     0 SECTION LOCAL  DEFAULT    3 
    17: 0000000000000368     0 SECTION LOCAL  DEFAULT    4 
    18: 0000000000000406     0 SECTION LOCAL  DEFAULT    5 
    19: 0000000000000420     0 SECTION LOCAL  DEFAULT    6 
    20: 0000000000000440     0 SECTION LOCAL  DEFAULT    7 
    21: 00000000000004e8     0 SECTION LOCAL  DEFAULT    8 
    22: 0000000000000500     0 SECTION LOCAL  DEFAULT    9 
    23: 0000000000000520     0 SECTION LOCAL  DEFAULT   10 
    24: 0000000000000540     0 SECTION LOCAL  DEFAULT   11 
    25: 0000000000000550     0 SECTION LOCAL  DEFAULT   12 
    26: 0000000000000660     0 SECTION LOCAL  DEFAULT   13 
    27: 0000000000000669     0 SECTION LOCAL  DEFAULT   14 
    28: 0000000000000674     0 SECTION LOCAL  DEFAULT   15 
    29: 00000000000006a0     0 SECTION LOCAL  DEFAULT   16 
    30: 0000000000200e10     0 SECTION LOCAL  DEFAULT   17 
    31: 0000000000200e18     0 SECTION LOCAL  DEFAULT   18 
    32: 0000000000200e20     0 SECTION LOCAL  DEFAULT   19 
    33: 0000000000200fe0     0 SECTION LOCAL  DEFAULT   20 
    34: 0000000000201000     0 SECTION LOCAL  DEFAULT   21 
    35: 0000000000201020     0 SECTION LOCAL  DEFAULT   22 
    36: 0000000000201028     0 SECTION LOCAL  DEFAULT   23 
    37: 0000000000000000     0 SECTION LOCAL  DEFAULT   24 
    38: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
    39: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
    40: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@@GLIBC_2.2.5
    41: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
    42: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    43: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    44: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func
    45: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
    46: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    47: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    48: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@@GLIBC_2.2
    49: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init

Histogram for `.gnu.hash' bucket list length (total of 3 buckets):
 Length  Number     % of total  Coverage
      0  0          (  0.0%)
      1  0          (  0.0%)      0.0%
      2  2          ( 66.7%)     57.1%
      3  1          ( 33.3%)    100.0%

Version symbols section '.gnu.version' contains 13 entries:
 Addr: 0000000000000406  Offset: 0x000406  Link: 3 (.dynsym)
  000:   0 (*local*)       0 (*local*)       2 (GLIBC_2.2.5)   0 (*local*)    
  004:   0 (*local*)       2 (GLIBC_2.2.5)   1 (*global*)      1 (*global*)   
  008:   1 (*global*)      1 (*global*)      1 (*global*)      1 (*global*)   
  00c:   1 (*global*)   

Version needs section '.gnu.version_r' contains 1 entry:
 Addr: 0x0000000000000420  Offset: 0x000420  Link: 4 (.dynstr)
  000000: Version: 1  File: libc.so.6  Cnt: 1
  0x0010:   Name: GLIBC_2.2.5  Flags: none  Version: 2

Displaying notes found in: .note.gnu.build-id
  Owner                 Data size	Description
  GNU                  0x00000014	NT_GNU_BUILD_ID (unique build ID bitstring)
    Build ID: 3a6987a7f71f453637ab35daa89ec141fba9026b

```
</details>

* `--strip-unneeded` 删除重定位不需要的符号

```shell
$ strip --strip-unneeded libtest.so
$ ll | grep libtest.so
-rwxrwxr-x 1 nolehuan nolehuan  6072 12月  2 13:49 libtest.so*
$ readelf -s libtest.so

Symbol table '.dynsym' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)
     6: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   22 _edata
     7: 0000000000201030     0 NOTYPE  GLOBAL DEFAULT   23 _end
     8: 000000000000063d    32 FUNC    GLOBAL DEFAULT   12 add
     9: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    10: 0000000000000500     0 FUNC    GLOBAL DEFAULT    9 _init
    11: 0000000000000660     0 FUNC    GLOBAL DEFAULT   13 _fini
    12: 000000000000062a    19 FUNC    GLOBAL DEFAULT   12 func
```

符号表 .symtab 通常包含动态符号表中 .dynsym 的全部符号，在程序编译过程中必须存在，运行时不必存在

重定位通常由链接器在链接时完成，也可以在加载时由重定位加载程序完成，或者在运行时由正在运行的程序本身完成

#### gcc

`-s` 效果与 `strip -s` 相同，删除符号表与重定位信息
```shell
$ gcc -fPIC -shared -o libtest.so libtest.c -g -s
$ ll | grep libtest.so
-rwxrwxr-x 1 nolehuan nolehuan  6072 12月  2 15:22 libtest.so*
```

`gcc` 去掉参数 `-g` 相当于 `strip --strip-debug`

#### cmake

不添加 `-g` 编译选项，编译类型设置 Release
```shell
add_compile_options(-std=c++11 -Wall -O2 -g)
set(CMAKE_BUILD_TYPE Debug)
```

`make install/strip` 命令安装
```shell
$ make install
$ ll
-rwxr-xr-x 1 nolehuan nolehuan 36088 12月  2 19:23 demo*
$ make install/strip
$ ll
-rwxr-xr-x 1 nolehuan nolehuan 10216 12月  2 19:23 demo*
```

#### LTO

Link Time Optimization 链接时优化

在链接阶段进行分析和优化，实现跨编译单元的优化
* 函数内敛决策
* 分析程序数据流与控制流，检测并消除死代码
* 跨模块常量传播
* 跨模块重复代码消除

```shell
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -flto")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -flto")
```

```shell
$ ll
-rwxrwxr-x 1 nolehuan nolehuan 36088 12月  3 10:16 demo*
$ vim CMakeLists.txt
$ cd build/
$ make
$ ll
-rwxrwxr-x 1 nolehuan nolehuan 16968 12月  3 10:16 demo*
```

#### GC sections

链接器对无用 section 进行垃圾回收
编译器参数 `-ffunction-sections` 与 `-fdata-sections`，将每个变量和函数分别放到独立的 section 中

bar.c
```c
#include <stdio.h>

void bar() {
  printf("bar\n");
}

void baz() {
  printf("baz\n");
}
```
foo.c
```c
void bar();

void foo() {
  bar();
}
```
vis.map
```
{
  global:
    foo;
  local:
    *;
};
```
编译
```shell
$ gcc -c -ffunction-sections foo.c
$ gcc -c -ffunction-sections bar.c
$ gcc -Wl,--version-script,vis.map -shared -o libfoo.so foo.o bar.o
$ nm -D libfoo.so 
                 w __cxa_finalize
000000000000054a T foo
                 w __gmon_start__
                 w _ITM_deregisterTMCloneTable
                 w _ITM_registerTMCloneTable
                 U puts
$ objdump -d libfoo.so
...
000000000000054a <foo>:
... 
000000000000055b <bar>:
...
000000000000056e <baz>:
...
$ gcc -Wl,--version-script,vis.map -Wl,--gc-sections -shared -o libfoo.so foo.o bar.o
$ objdump -d libfoo.so
...
000000000000054a <foo>:
...
000000000000055b <bar>:
...
```

### upx

开源跨平台的压缩壳，文件大于 40KB 可压缩

```c
$ cat demo.c 
#include <stdio.h>
#include <time.h>

void getNow() {
  time_t now;
  time(&now);
  printf("%ld\n", now);
}

int main() {
  getNow();
  return 0;
}
```
`-1` 快速压缩
```shell
$ gcc -static -o demo demo.c
$ ll -h
-rwxrwxr-x 1 nolehuan nolehuan 826K 12月  3 09:49 demo*
-rw-rw-r-- 1 nolehuan nolehuan  148 12月  3 09:49 demo.c
$ upx -1 demo
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2017
UPX 3.94        Markus Oberhumer, Laszlo Molnar & John Reiser   May 12th 2017

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
    845304 ->    377620   44.67%   linux/amd64   demo                          

Packed 1 file.
$ ./demo
1701568199
$ ll -h
-rwxrwxr-x 1 nolehuan nolehuan 369K 12月  3 09:49 demo*
```

### 符号局部化

* static 关键字

控制编译器将变量存储在静态存储区

修饰局部变量，仅初始化一次，延长生命周期至程序运行结束u

修饰全局变量，只能在本文件中访问，不可以 extern 外部声明

修饰函数，只能在本文件调用

* 使用 version script 控制符号可见性，指定动态库导出符号

```
{
  global:
    foo;
  local:
    *;
};
```

* hidden

`gcc` 编译参数 `-fvisibility=default|internal|hidden|protected`
`hidden` 符号对外隐藏，只能在同一共享对象中引用
`default` 具有外部链接性，可能被重写
`internal` 外部无法直接引用，可以通过指针间接引用
`protected` 可以引用，无法重写
对函数设置 `__attribute__((visibility("default")))` 优先级高于编译参数

<details>
<summary>code</summary>

hello.h
```c++
#pragma once

#include <iostream>

class Hello {
  public:
    void print();
};

void __attribute__((visibility("hidden"))) foo();

// void foo();
```
hello.cpp
```c++
#include "hello.h"

void Hello::print() {
  std::cout << "hello" << std::endl;
}

void foo() {
  printf("foo\n");
}
```
main.cpp
```c++
#include "hello.h"

int main(){
  std::cout << "demo" << std::endl;
  Hello h;
  h.print();
  foo();
  return 0;
}
```
</details>

```shell
$ g++ -fPIC -shared -o libhello.so hello.cpp -g
$ g++ -o main main.cpp -L . -lhello
$ ./main 
demo
hello
foo
$ vim hello.h
$ g++ -fPIC -shared -o libhello.so hello.cpp -g
$ g++ -o main main.cpp -L . -lhello
/tmp/cch6S9Rd.o: In function `main':
main.cpp:(.text+0x4c): undefined reference to `foo()'
/usr/bin/ld: main: hidden symbol `_Z3foov' isn't defined
/usr/bin/ld: final link failed: Bad value
collect2: error: ld returned 1 exit status
```

### 动态库合并

合并后可删除部分动态符号表项、程序链接表（Procedure Linkage Table，PLT）表项、全局偏移表（Global Offset Table，GOT）表项


### 参考

Ziegler, A. ,  Geus, J. ,  Heinloth, B. , Timo HÖnig, &  Lohmann, D. . (2019). Honey, i shrunk the elfs: lightweight binary tailoring of shared libraries. ACM Transactions on Embedded Computing Systems, 18(5s), 1-23.
https://zhuanlan.zhihu.com/p/640026481
https://zhuanlan.zhihu.com/p/596165659
https://zhuanlan.zhihu.com/p/72475595
https://blog.csdn.net/qq_21438461/article/details/130243914
https://stackoverflow.com/questions/76138138/remove-unused-functions-from-shared-library
