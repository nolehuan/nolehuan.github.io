---
title: shell
date: 2023-11-28 21:30:29
abstracts: shell
tags:
  - shell
categories:
  - script
cover: ''
feature: false
mathjax: false
---


#### du

统计当前目录以及子目录所占磁盘空间大小
`-h` `–human-readable`
`-s` 仅显示当前目录

```shell
$ du -h
68K	./src/so
144K	./src
380K	./upx
32K	./op
1.2M	.
```

#### ps

* ps -aux

```shell
$ ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.2  0.1 225748  9368 ?        Ss   21:28   0:01 /sbin/init splash
root         2  0.0  0.0      0     0 ?        S    21:28   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        I<   21:28   0:00 [rcu_gp]
```

`a` 显示终端上的所有进程
`u` 显示进程用户
`x` 显示所有终端的进程

* ps -elf

```shell
$ ps -elf
F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root         1     0  0  80   0 - 56437 -      21:28 ?        00:00:02 /sbin/init splash
1 S root         2     0  0  80   0 -     0 -      21:28 ?        00:00:00 [kthreadd]
1 I root         3     2  0  60 -20 -     0 -      21:28 ?        00:00:00 [rcu_gp]
```

`e` 显示系统内的所有进程信息
`l` 使用长格式显示进程信息
`f` 使用完整格式显示进程信息

#### pidof

查找指定进程名的进程 PID
```shell
$ ps -elf | grep hexo
0 S nolehuan  3670  3581  0  80   0 - 243195 ep_pol 21:41 pts/1   00:00:02 hexo
0 S nolehuan  4297  3502  0  80   0 -  4221 pipe_w 21:52 pts/0    00:00:00 grep --color=auto hexo
$ pidof hexo
3670
```

#### find

`find -name '*.sh'` 查找当前目录及子目录下 `sh` 结尾的文件

#### kill

`kill -9 pid` 杀死进程

#### mkdir

`-p` 递归创建文件夹

#### ln

创建软链接

```shell
$ ln -snf src lsrc
$ ll
total 36
lrwxrwxrwx 1 nolehuan nolehuan    3 12月  9 16:06 lsrc -> src/
drwxrwxr-x 3 nolehuan nolehuan 4096 12月  2 20:20 src/
```

#### tar

`tar czvf dst src` 压缩

`tar xzvf src` 解压缩

#### top

`1` `t` `t` `m` `m`

```shell
$ top

top - 15:50:27 up  2:46,  1 user,  load average: 0.32, 0.38, 0.49
Tasks: 358 total,   1 running, 290 sleeping,   0 stopped,   0 zombie
%Cpu0  :   0.0/0.0     0[                                                                          ]
%Cpu1  :   2.6/0.0     3[||                                                                        ]
%Cpu2  :   2.6/0.0     3[||                                                                        ]
%Cpu3  :   2.5/0.0     2[||                                                                        ]
%Cpu4  :   0.0/0.0     0[                                                                          ]
%Cpu5  :   2.7/0.0     3[||                                                                        ]
%Cpu6  :  15.0/7.5    22[|||||||||||||||||                                                         ]
%Cpu7  :   0.0/0.0     0[                                                                          ]
KiB Mem : 52.5/7886744  [||||||||||||||||||||||||||||||||||||||                                    ]
KiB Swap:  0.1/10000380 [                                                                          ]

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 1603 nolehuan  20   0 4453592 426660 126952 S  13.5  5.4  12:35.93 gnome-shell
10574 nolehuan  20   0   46800   4068   3340 R   8.1  0.1   0:00.10 top
 2867 nolehuan  20   0 3077040 386272 122580 S   5.4  4.9   2:06.11 Isolated Web Co
 1625 nolehuan   9 -11 2910064  17836  13740 S   2.7  0.2   9:30.49 pulseaudio
 1646 nolehuan  20   0  360204  15064   6564 S   2.7  0.2   0:55.57 ibus-daemon
 2622 nolehuan  20   0 5061640 803796 302676 S   2.7 10.2  34:42.42 firefox
 3894 nolehuan  20   0  761120  71816  44236 S   2.7  0.9   0:14.24 terminator
 4988 nolehuan  20   0 32.431g 208660 115372 S   2.7  2.6   3:28.31 code
```

`top -H -p` 查看指定进程的线程

#### rsync

`rsync -av -P src dst` 复制文件

```shell
$ rsync -av -P op/ ./tmp
sending incremental file list
created directory ./tmp
./
bar.c
             89 100%    0.00kB/s    0:00:00 (xfr#1, to-chk=5/7)
bar.o
          1,944 100%    1.85MB/s    0:00:00 (xfr#2, to-chk=4/7)
foo.c
             37 100%   36.13kB/s    0:00:00 (xfr#3, to-chk=3/7)
foo.o
          1,496 100%    1.43MB/s    0:00:00 (xfr#4, to-chk=2/7)
libfoo.so
          7,952 100%    7.58MB/s    0:00:00 (xfr#5, to-chk=1/7)
vis.map
             40 100%   39.06kB/s    0:00:00 (xfr#6, to-chk=0/7)

sent 11,980 bytes  received 161 bytes  24,282.00 bytes/sec
total size is 11,558  speedup is 0.95
```

#### readelf

elf 文件
* 可执行文件 .out
* 共享目标文件 .a
* 可重定位文件 .so

参数
`-a` 显示全部信息
`-h` 显示文件头信息 ELF Header
`-l` 显示程序头信息 Program Headers
`-S` 显示节头信息 Section Headers
`-g` 显示节组信息
`-s` 显示符号表段中的项 Symbol table
`-n` 显示内核注释信息
`-r` 显示可重定位段的信息 Relocation section
`-d` 显示动态段信息 Dynamic section
`-V` 显示版本段信息

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
```
</details>

#### sed

`sed -i 's/from/to/g' filename` 替换文本内容

```shell
$ echo "ababa" > tmp
$ cat tmp
ababa
$ sed -i 's/a/c/g' tmp 
$ cat tmp
cbcbc
```

#### stress

压测工具

* CPU 压测 sqrt()
```shell
$ stress -c 10 -t 10
stress: info: [17965] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd
stress: info: [17965] successful run completed in 10s
```

`-c` 进程数
`-t` 压测持续时间

* 内存压测 malloc()/free()
```shell
$ stress -m 3 --vm-bytes 521M --vm-keep -t 10
stress: info: [18012] dispatching hogs: 0 cpu, 0 io, 3 vm, 0 hdd
qstress: info: [18012] successful run completed in 10s
```

`-m` 进程数
`--vm-bytes` 申请内存大小
`--vm-keep` 持续占用内存，默认频繁申请释放内存

* 磁盘压测 write()/unlink()
```shell
$ stress -d 3 --hdd-bytes 512M -t 10 & sudo iotop
```

`-d` 进程数
`--hdd-bytes` 写数据量

* IO 压测 sync()
```shell
$ stress -i 3 -t 10 & sudo iotop
```

#### iperf

网络性能测试工具，带宽测试

服务端 `iperf -s`
客户端 `iperf -c <host> -w 2m -t 30s -i 1`
`host` 服务端 IP 地址
`-w` TCP 窗口大小
`-i` 带宽报告间隔时间

#### diff

```shell
$ echo -e "a\nb\nc\nd" > a
$ cat  a
a
b
c
d
$ echo -e "aa\nb\nd\ne" > b
$ cat b
aa
b
d
e
$ diff a b
1c1
< a
---
> aa
3d2
< c
4a4
> e
```

`echo -e` 激活转义字符
`a` add
`c` change
`d` delete
`<` 左文件文件内容
`1c1` 修改左文件第一行与右文件匹配

#### curl

抓取百度首页内容
```shell
$ curl -o baidu.html http://www.baidu.com
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2381  100  2381    0     0  41771      0 --:--:-- --:--:-- --:--:-- 41771
$ ll```
-rw-rw-r--  1 nolehuan nolehuan 2381 12月 10 17:28 baidu.html
```

`-o` 输出文件名称

#### awk

```shell
$ echo -e "a,b,c\nd,e,f" > tmp
$ cat tmp
a,b,c
d,e,f
$ cat tmp | awk -F, '{print $2}'
b
e
```

`-F` 分隔符，默认空格

#### dmesg

内核相关信息，启动日志，OOM 日志等
```shell
$ dmesg -T | tail -f -n 5
[日 12月 10 17:45:14 2023] wlp0s20f3: authenticated
[日 12月 10 17:45:14 2023] wlp0s20f3: associate with 78:44:fd:1e:88:ac (try 1/3)
[日 12月 10 17:45:14 2023] wlp0s20f3: RX ReassocResp from 78:44:fd:1e:88:ac (capab=0x1011 status=0 aid=1)
[日 12月 10 17:45:14 2023] wlp0s20f3: associated
[日 12月 10 18:05:56 2023] perf: interrupt took too long (6190 > 6163), lowering kernel.perf_event_max_sample_rate to 32250
```

`-T` 显示易读时间戳
`-f` 监视文件增长

#### grep

查询当前目录以及子目录下文件中的内容
```shell
$ grep -Rni now
tmp/tmp:1:now
demo.c:4:void getNow() {
demo.c:5:  time_t now;
demo.c:6:  time(&now);
demo.c:7:  printf("%ld\n", now);
demo.c:11:  getNow();
```

`-R` 递归查找
`-n` 显示行号
`-i` 忽略大小写

#### pgrep

根据进程名来查询进程 ID
```shell
$ ./ptest & pgrep ptest
[1] 13099
13099
$ fg 1
./ptest
```

#### ethtool

查看网卡信息
```shell
$ ethtool -i wlp0s20f3
driver: iwlwifi
version: 5.4.0-150-generic
firmware-version: 46.6bf1df06.0
expansion-rom-version: 
bus-info: 0000:00:14.3
supports-statistics: yes
supports-test: no
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: no
```

`-i` 查看驱动信息

#### iftop

```shell
$ sudo iftop -i wlp0s20f3
interface: wlp0s20f3
IP address is: 192.168.99.101
MAC address is: 08:71:90:3c:dc:05
$ sudo iftop -i wlp0s20f3 -t -s 3
interface: wlp0s20f3
IP address is: 192.168.99.101
MAC address is: 08:71:90:3c:dc:05
Listening on wlp0s20f3
   # Host name (port/service if enabled)            last 2s   last 10s   last 40s cumulative
--------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------
Total send rate:                                         0b         0b         0b
Total receive rate:                                      0b         0b         0b
Total send and receive rate:                             0b         0b         0b
--------------------------------------------------------------------------------------------
Peak rate (sent/received/total):                         0b         0b         0b
Cumulative (sent/received/total):                        0B         0B         0B
============================================================================================
```

`-i` 指定监控网卡
`-t` 文本界面
`-s` n 秒后打印一次文本输出后退出

#### tee

将用户输入保存到文件
```shell
$ tee tmp
x
x
y
y
^C
$ cat tmp
x
y
```

`-a` 追加

#### objdump

目标文件反汇编工具
`-f` 显示文件头信息
`-d` 反汇编可执行节
`-D` 反汇编所有节
`-h` 显示节头内容
`-t` 显示符号表内容

```shell
$ objdump -f p

p:     file format elf64-x86-64
architecture: i386:x86-64, flags 0x00000150:
HAS_SYMS, DYNAMIC, D_PAGED
start address 0x0000000000000c10

$ objdump -d p

p:     file format elf64-x86-64


Disassembly of section .init:

0000000000000b18 <_init>:
 b18:	48 83 ec 08          	sub    $0x8,%rsp
 b1c:	48 8b 05 c5 24 20 00 	mov    0x2024c5(%rip),%rax        # 202fe8 <__gmon_start__>
 b23:	48 85 c0             	test   %rax,%rax
 b26:	74 02                	je     b2a <_init+0x12>
 b28:	ff d0                	callq  *%rax
 b2a:	48 83 c4 08          	add    $0x8,%rsp
 b2e:	c3                   	retq
...
```

#### dd

复制文件
```shell
$ dd if=a of=b
0+1 records in
0+1 records out
8 bytes copied, 0.000414869 s, 19.3 kB/s
```

`if` 输入文件或设备
`of` 输出文件或设备
