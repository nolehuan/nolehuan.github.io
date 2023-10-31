---
title: so
date: 2023-11-26 19:30:29
abstracts: 使用 mv 与 cp 命令更新运行中的动态链接库是否会发生问题？
tags:
  - cpp
  - linux
  - so
categories:
  - coding
cover: ''
feature: false
mathjax: false
---

### 使用 mv 与 cp 命令更新运行中的动态链接库是否会发生问题？

`cp` 命令更新运行中的动态链接库会导致程序 `core` 掉？

#### Linux 文件系统

Linux 文件系统中，每个文件对应一个 inode 与 dentry

##### inode

硬盘分为两个区域：数据区与 inode table，inode 存储文件元信息，可以用 `stat` 命令查看

```shell
$ stat test.cpp 
  File: test.cpp
  Size: 458       	Blocks: 8          IO Block: 4096   regular file
Device: 10307h/66311d	Inode: 535078      Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/nolehuan)   Gid: ( 1000/nolehuan)
Access: 2023-11-04 15:20:56.725957003 +0800
Modify: 2022-11-24 22:25:10.659748611 +0800
Change: 2022-11-24 22:25:10.659748611 +0800
 Birth: -
```
`Links: 1` 记录硬链接计数，硬链接仅文件名不同，inode 号相同；软链接文件数据块中存放对应文件的路径

`ls -i` 命令查看文件 inode 编号
```shell
$ ls -i test.cpp 
535078 test.cpp
```

##### dentry

directory entry 记录文件的目录信息与 inode 指针，存在于内存中

程序运行时，进程 pcb 中保存一份文件描述符表，文件描述符对应进程已打开文件的 file 结构体，file 结构体中包括文件打开时的标志位、引用计数和 dentry 指针等信息，通过 dentry 指针找到文件对应 inode 进而操作文件

#### cp mv rm 原理

执行 `cp` 命令时 inode 号分配规则
* 目标文件存在则 inode 保持文件覆盖前的编号
* 目标文件不存在则分配一个未使用的 inode 号，并添加至 inode table

```shell
$ touch a b && ls -i a b
548015 a  560350 b
$ cp a b && ls -i a b
548015 a  560350 b
$ cp b c && ls -i b c
560350 b  584072 c
```

执行 `mv` 命令时，所在文件系统相同则保持源文件 inode 号
```shell
$ touch a b && ls -i a b
583955 a  584095 b
$ mv a b && ls -i b
583955 b
$ mv b c && ls -i c
583955 c
```

`cp` 命令执行步骤
* inode 号分配
* 新建 dentry 并指向 inode
* 复制数据至 block 中

`rm` 命令执行步骤
* 递减硬链接计数，计数归零时释放 inode 号
* 将数据块挂至空闲链表
* 删除目录映射表中的相关行

`mv` 命令执行步骤
* 源文件与目标文件所在文件系统相同：新建 dentry，删除原 dentry，对 inode 无影响，不移动数据
* 源文件与目标文件所在文件系统不同：`cp` + `rm`

`strace` 命令跟踪 `cp` 执行过程
```shell
$ cat a
file a
$ cat b
file b
$ strace cp a b
...
openat(AT_FDCWD, "a", O_RDONLY)         = 3
fstat(3, {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
openat(AT_FDCWD, "b", O_WRONLY|O_TRUNC) = 4
fstat(4, {st_mode=S_IFREG|0664, st_size=0, ...}) = 0
...
read(3, "file a\n", 131072)             = 7
write(4, "file a\n", 7)                 = 7
read(3, "", 131072)                     = 0
close(4)                                = 0
close(3)                                = 0
...
```
<details>
<summary>details</summary>

```shell
$ strace cp a b
execve("/bin/cp", ["cp", "a", "b"], 0x7ffccded9d50 /* 78 vars */) = 0
brk(NULL)                               = 0x55a404a95000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/haswell/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls/haswell/x86_64", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/haswell/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls/haswell", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls/x86_64", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/haswell/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/haswell/x86_64", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/haswell/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/haswell", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/x86_64", 0x7ffcc58a7250) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib", {st_mode=S_IFDIR|0755, st_size=36864, ...}) = 0
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=223218, ...}) = 0
mmap(NULL, 223218, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f89809af000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20b\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=154832, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f89809ad000
mmap(NULL, 2259152, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f8980595000
mprotect(0x7f89805ba000, 2093056, PROT_NONE) = 0
mmap(0x7f89807b9000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x24000) = 0x7f89807b9000
mmap(0x7f89807bb000, 6352, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f89807bb000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libacl.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libacl.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340\33\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=31232, ...}) = 0
mmap(NULL, 2126336, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f898038d000
mprotect(0x7f8980394000, 2093056, PROT_NONE) = 0
mmap(0x7f8980593000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6000) = 0x7f8980593000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libattr.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libattr.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260\20\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=18680, ...}) = 0
mmap(NULL, 2113752, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f8980188000
mprotect(0x7f898018c000, 2093056, PROT_NONE) = 0
mmap(0x7f898038b000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x3000) = 0x7f898038b000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\240\35\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=2030928, ...}) = 0
mmap(NULL, 4131552, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f897fd97000
mprotect(0x7f897ff7e000, 2097152, PROT_NONE) = 0
mmap(0x7f898017e000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1e7000) = 0x7f898017e000
mmap(0x7f8980184000, 15072, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f8980184000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libpcre.so.3", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpcre.so.3", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0 \25\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=460728, ...}) = 0
mmap(NULL, 2556168, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f897fb26000
mprotect(0x7f897fb96000, 2093056, PROT_NONE) = 0
mmap(0x7f897fd95000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6f000) = 0x7f897fd95000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libdl.so.2", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\16\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=14560, ...}) = 0
mmap(NULL, 2109712, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f897f922000
mprotect(0x7f897f925000, 2093056, PROT_NONE) = 0
mmap(0x7f897fb24000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x2000) = 0x7f897fb24000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libpthread.so.0", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000b\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=144976, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f89809ab000
mmap(NULL, 2221184, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f897f703000
mprotect(0x7f897f71d000, 2093056, PROT_NONE) = 0
mmap(0x7f897f91c000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x19000) = 0x7f897f91c000
mmap(0x7f897f91e000, 13440, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f897f91e000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f89809a8000
arch_prctl(ARCH_SET_FS, 0x7f89809a8800) = 0
mprotect(0x7f898017e000, 16384, PROT_READ) = 0
mprotect(0x7f897f91c000, 4096, PROT_READ) = 0
mprotect(0x7f897fb24000, 4096, PROT_READ) = 0
mprotect(0x7f897fd95000, 4096, PROT_READ) = 0
mprotect(0x7f898038b000, 4096, PROT_READ) = 0
mprotect(0x7f8980593000, 4096, PROT_READ) = 0
mprotect(0x7f89807b9000, 4096, PROT_READ) = 0
mprotect(0x55a403631000, 4096, PROT_READ) = 0
mprotect(0x7f89809e6000, 4096, PROT_READ) = 0
munmap(0x7f89809af000, 223218)          = 0
set_tid_address(0x7f89809a8ad0)         = 5123
set_robust_list(0x7f89809a8ae0, 24)     = 0
rt_sigaction(SIGRTMIN, {sa_handler=0x7f897f708cb0, sa_mask=[], sa_flags=SA_RESTORER|SA_SIGINFO, sa_restorer=0x7f897f715980}, NULL, 8) = 0
rt_sigaction(SIGRT_1, {sa_handler=0x7f897f708d50, sa_mask=[], sa_flags=SA_RESTORER|SA_RESTART|SA_SIGINFO, sa_restorer=0x7f897f715980}, NULL, 8) = 0
rt_sigprocmask(SIG_UNBLOCK, [RTMIN RT_1], NULL, 8) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
statfs("/sys/fs/selinux", 0x7ffcc58a7b60) = -1 ENOENT (No such file or directory)
statfs("/selinux", 0x7ffcc58a7b60)      = -1 ENOENT (No such file or directory)
brk(NULL)                               = 0x55a404a95000
brk(0x55a404ab6000)                     = 0x55a404ab6000
openat(AT_FDCWD, "/proc/filesystems", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0444, st_size=0, ...}) = 0
read(3, "nodev\tsysfs\nnodev\ttmpfs\nnodev\tbd"..., 1024) = 393
read(3, "", 1024)                       = 0
close(3)                                = 0
access("/etc/selinux/config", F_OK)     = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=5513488, ...}) = 0
mmap(NULL, 5513488, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f897f1c0000
close(3)                                = 0
geteuid()                               = 1000
stat("b", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
stat("a", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
stat("b", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
openat(AT_FDCWD, "a", O_RDONLY)         = 3
fstat(3, {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
openat(AT_FDCWD, "b", O_WRONLY|O_TRUNC) = 4
fstat(4, {st_mode=S_IFREG|0664, st_size=0, ...}) = 0
fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0
mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f89809c4000
read(3, "file a\n", 131072)             = 7
write(4, "file a\n", 7)                 = 7
read(3, "", 131072)                     = 0
close(4)                                = 0
close(3)                                = 0
munmap(0x7f89809c4000, 139264)          = 0
lseek(0, 0, SEEK_CUR)                   = -1 ESPIPE (Illegal seek)
close(0)                                = 0
close(1)                                = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++
```
</details>

`strace` 命令跟踪 `mv` 执行过程
```shell
$ cat a
file a
$ cat b
file b
$ strace mv a b
...
rename("a", "b")                        = 0
...
```
<details>
<summary>details</summary>

```shell
$ strace mv a b
execve("/bin/mv", ["mv", "a", "b"], 0x7fffb37729b0 /* 78 vars */) = 0
brk(NULL)                               = 0x55b38eebf000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/haswell/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls/haswell/x86_64", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/haswell/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls/haswell", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls/x86_64", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/tls/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/tls", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/haswell/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/haswell/x86_64", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/haswell/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/haswell", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/x86_64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib/x86_64", 0x7ffc2d702d20) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/opt/ros/melodic/lib/libselinux.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
stat("/opt/ros/melodic/lib", {st_mode=S_IFDIR|0755, st_size=36864, ...}) = 0
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=223218, ...}) = 0
mmap(NULL, 223218, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f615a44d000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20b\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=154832, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f615a44b000
mmap(NULL, 2259152, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f615a033000
mprotect(0x7f615a058000, 2093056, PROT_NONE) = 0
mmap(0x7f615a257000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x24000) = 0x7f615a257000
mmap(0x7f615a259000, 6352, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f615a259000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libacl.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libacl.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340\33\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=31232, ...}) = 0
mmap(NULL, 2126336, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f6159e2b000
mprotect(0x7f6159e32000, 2093056, PROT_NONE) = 0
mmap(0x7f615a031000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6000) = 0x7f615a031000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libattr.so.1", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libattr.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260\20\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=18680, ...}) = 0
mmap(NULL, 2113752, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f6159c26000
mprotect(0x7f6159c2a000, 2093056, PROT_NONE) = 0
mmap(0x7f6159e29000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x3000) = 0x7f6159e29000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\240\35\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=2030928, ...}) = 0
mmap(NULL, 4131552, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f6159835000
mprotect(0x7f6159a1c000, 2097152, PROT_NONE) = 0
mmap(0x7f6159c1c000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1e7000) = 0x7f6159c1c000
mmap(0x7f6159c22000, 15072, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f6159c22000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libpcre.so.3", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpcre.so.3", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0 \25\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=460728, ...}) = 0
mmap(NULL, 2556168, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f61595c4000
mprotect(0x7f6159634000, 2093056, PROT_NONE) = 0
mmap(0x7f6159833000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6f000) = 0x7f6159833000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libdl.so.2", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\16\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=14560, ...}) = 0
mmap(NULL, 2109712, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f61593c0000
mprotect(0x7f61593c3000, 2093056, PROT_NONE) = 0
mmap(0x7f61595c2000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x2000) = 0x7f61595c2000
close(3)                                = 0
openat(AT_FDCWD, "/opt/ros/melodic/lib/libpthread.so.0", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000b\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=144976, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f615a449000
mmap(NULL, 2221184, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f61591a1000
mprotect(0x7f61591bb000, 2093056, PROT_NONE) = 0
mmap(0x7f61593ba000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x19000) = 0x7f61593ba000
mmap(0x7f61593bc000, 13440, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f61593bc000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f615a446000
arch_prctl(ARCH_SET_FS, 0x7f615a446800) = 0
mprotect(0x7f6159c1c000, 16384, PROT_READ) = 0
mprotect(0x7f61593ba000, 4096, PROT_READ) = 0
mprotect(0x7f61595c2000, 4096, PROT_READ) = 0
mprotect(0x7f6159833000, 4096, PROT_READ) = 0
mprotect(0x7f6159e29000, 4096, PROT_READ) = 0
mprotect(0x7f615a031000, 4096, PROT_READ) = 0
mprotect(0x7f615a257000, 4096, PROT_READ) = 0
mprotect(0x55b38e8b8000, 4096, PROT_READ) = 0
mprotect(0x7f615a484000, 4096, PROT_READ) = 0
munmap(0x7f615a44d000, 223218)          = 0
set_tid_address(0x7f615a446ad0)         = 6068
set_robust_list(0x7f615a446ae0, 24)     = 0
rt_sigaction(SIGRTMIN, {sa_handler=0x7f61591a6cb0, sa_mask=[], sa_flags=SA_RESTORER|SA_SIGINFO, sa_restorer=0x7f61591b3980}, NULL, 8) = 0
rt_sigaction(SIGRT_1, {sa_handler=0x7f61591a6d50, sa_mask=[], sa_flags=SA_RESTORER|SA_RESTART|SA_SIGINFO, sa_restorer=0x7f61591b3980}, NULL, 8) = 0
rt_sigprocmask(SIG_UNBLOCK, [RTMIN RT_1], NULL, 8) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
statfs("/sys/fs/selinux", 0x7ffc2d703630) = -1 ENOENT (No such file or directory)
statfs("/selinux", 0x7ffc2d703630)      = -1 ENOENT (No such file or directory)
brk(NULL)                               = 0x55b38eebf000
brk(0x55b38eee0000)                     = 0x55b38eee0000
openat(AT_FDCWD, "/proc/filesystems", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0444, st_size=0, ...}) = 0
read(3, "nodev\tsysfs\nnodev\ttmpfs\nnodev\tbd"..., 1024) = 393
read(3, "", 1024)                       = 0
close(3)                                = 0
access("/etc/selinux/config", F_OK)     = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=5513488, ...}) = 0
mmap(NULL, 5513488, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f6158c5e000
close(3)                                = 0
geteuid()                               = 1000
ioctl(0, TCGETS, {B38400 opost isig icanon echo ...}) = 0
stat("b", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
lstat("a", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
lstat("b", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
geteuid()                               = 1000
stat("b", {st_mode=S_IFREG|0664, st_size=7, ...}) = 0
geteuid()                               = 1000
getegid()                               = 1000
getuid()                                = 1000
getgid()                                = 1000
access("b", W_OK)                       = 0
rename("a", "b")                        = 0
lseek(0, 0, SEEK_CUR)                   = -1 ESPIPE (Illegal seek)
close(0)                                = 0
close(1)                                = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++
```
</details>

#### 动态库

应用程序使用动态库过程
* 应用程序通过 `dlopen` 打开 so 时，内核通过 `mmap` 将 so 加载到进程地址空间，对应虚拟内存区域 VMA 中的页 page
* 加载器 loader 将 so 里引用的外部符号解析成真正的虚拟地址
* 当 so 被 `cp` trunc 时，内核将旧 so 文件从虚拟内存对应物理内存中清除
* 当应用程序运行到 so 中的代码时，产生缺页中断
* 内核重新加载新 so
  * 如果文件偏移量超出新 so 地址范围，将产生 bus error
  * 如果新 so 依赖外部符号，此时全局符号表没有经过重新解析，将产生段错误
  * 如果新 so 不依赖外部符号，程序可以运行

#### 测试

库文件
```c++
#include <stdio.h>

void func(void) {
  printf("new lib\n");
  printf("old lib\n");
}

void add(void) {
  int a = 1, b = 2;
  int c = a + b;
}
```

测试文件
```c++
#include <dlfcn.h>
#include <stdio.h>

int main() {
  void* handle;
  void (*func)(void);
  void (*add)(void);

  handle = dlopen("libtest.so", RTLD_LAZY);
  if (!handle) {
    fprintf(stderr, "%s\n", dlerror());
    return -1;
  }

  func = dlsym(handle, "func");
  if (dlerror() != NULL) {
    fprintf(stderr, "%s\n", dlerror());
    return -1;
  }
  func();

  add = dlsym(handle, "add");
  if (dlerror() != NULL) {
    fprintf(stderr, "%s\n", dlerror());
    return -1;
  }
  add();

  dlclose(handle);
}

```

编译
```shell
$ gcc -fPIC -shared -o libtest.so libtest.c -g
$ gcc -o use_lib use_lib.c -ldl -g
```

`-g` 生成调试信息

`-fPIC` 生成位置无关代码(Position-Independent Code)，创建共享库时允许代码在任何内存地址执行而无需修改

报错解决，修改 LD_PRELOAD 无效
```shell
libtest.so: cannot open shared object file: No such file or directory
$ export LD_LIBRARY_PATH=$(pwd):$LD_LIBRARY_PATH
```

gdb 调试：函数调用处添加断点后，在另一终端 `cp` 替换库文件 so，回到 gdb `n` 继续单步运行
* func 函数断点调试

```shell
$ gdb use_lib
GNU gdb (Ubuntu 8.1.1-0ubuntu1) 8.1.1
...
Reading symbols from use_lib...done.
(gdb) b 20
Breakpoint 1 at 0x876: file use_lib.c, line 20.
(gdb) r
Starting program: /home/nolehuan/cpp/src/use_lib 

Breakpoint 1, main () at use_lib.c:20
20	  func();
(gdb) n

Program received signal SIGSEGV, Segmentation fault.
0x0000000000000536 in ?? ()
(gdb) bt
#0  0x0000000000000536 in ?? ()
#1  0x00007ffff75dc63a in func () at libtest.c:4
#2  0x000055555555487c in main () at use_lib.c:20
```

* add 函数断点调试

```shell
$ gdb use_lib
GNU gdb (Ubuntu 8.1.1-0ubuntu1) 8.1.1
...
Reading symbols from use_lib...done.
(gdb) b 27
Breakpoint 1 at 0x8c7: file use_lib.c, line 27.
(gdb) r
Starting program: /home/nolehuan/cpp/src/use_lib 
old lib

Breakpoint 1, main () at use_lib.c:27
27	  add();
(gdb) n
29	  dlclose(handle);
```

#### 结论

* `cp` 更新动态库，文件 inode 号不变，旧库文件被截断，重新加载至内存时，或因为文件偏移量超出新库文件地址范围产生 bus error 导致程序 core 掉，或因为动态库依赖外部符号但全局符号表没有经过重新解析产生段错误导致程序 core 掉
* `mv` 更新动态库，文件 inode 号改变，旧 inode 号依然存在，程序可以继续运行，必须重启服务才会加载新动态库

#### 正确做法

`rm` + `cp` 或 `mv` + `cp`

`rm` 时如果 ld.so 正在加载动态库，inode 不会立刻被删除，直至 ld.so 释放对动态库的引用，引用结束时才会真正删除 inode，程序不会崩溃，下次使用替换后的动态库

`mv` 仅改变文件名，inode 不变，新动态库使用新 inode，动态链接器 ld.so 使用原 inode 访问旧动态库

替换可执行程序时，系统会明令禁止 `cp` 操作，但系统不会阻止覆盖动态库
```shell
cp xxx xxx
cp: cannot create regular file `xxx`: Text file busy
```

Demand Paging 机制

程序运行时并非所有页都加载到内存中，只有需要访问时才会动态加载，Demand Paging 要求正在运行中的程序镜像不被意外修改，因此内核会锁定程序镜像的 inode

对于动态库而言，依靠用户态程序 ld.so 加载，无权锁定 inode

#### 相关内容

环境变量
* LIBRARY_PATH 静态链接库文件搜索路径
* LD_LIBRARY_PATH 动态链接库文件搜索路径

动态库 .so 与静态库 .a 同时存在时，`gcc/g++` 默认链接动态库，可使用参数 `-Wl,-Bstatic -lxxx` 或 `-Wl,-Bdynamic -lxxx` 指定链接静态/动态库

参数 `-static` 指定完全静态加载

静态库加载顺序
* `gcc -L` 参数
* LIBRARY_PATH
* 默认库目录

动态库加载顺序
* `gcc -L` 参数
* LD_LIBRARY_PATH
* /etc/ld.so.conf 配置文件中的路径
* 默认库目录

`ldd` 查看可执行程序依赖的共享库

动态库命名：`libxxxx.so.major.minor`，major 主版本号，minor 副版本号 

`gcc` 分阶段编译
```shell
$ gcc -E libtest.c -o libtest.i
$ gcc -S libtest.i -o libtest.s
$ gcc -c libtest.s -o libtest.o
```

生成静态库
```shell
$ ar cqs libtest.a libtest.o
```

`ar` archive
`c` 创建新的归档文件，已存在则覆盖
`q` 快速追加
`s` 构建对象索引文件，即符号表

`nm` 查看库中的函数
* T 函数在当前库中定义
* U 函数在其它库中定义被调用
* W 当前库中定义，被其它库中的函数覆盖

```shell
nm libtest.so
000000000000063d T add
0000000000201028 B __bss_start
0000000000201028 b completed.7698
                 w __cxa_finalize@@GLIBC_2.2.5
0000000000000550 t deregister_tm_clones
00000000000005e0 t __do_global_dtors_aux
0000000000200e18 t __do_global_dtors_aux_fini_array_entry
0000000000201020 d __dso_handle
0000000000200e20 d _DYNAMIC
0000000000201028 D _edata
0000000000201030 B _end
0000000000000660 T _fini
0000000000000620 t frame_dummy
0000000000200e10 t __frame_dummy_init_array_entry
0000000000000738 r __FRAME_END__
000000000000062a T func
0000000000201000 d _GLOBAL_OFFSET_TABLE_
                 w __gmon_start__
0000000000000674 r __GNU_EH_FRAME_HDR
0000000000000500 T _init
                 w _ITM_deregisterTMCloneTable
                 w _ITM_registerTMCloneTable
                 U puts@@GLIBC_2.2.5
0000000000000590 t register_tm_clones
0000000000201028 d __TMC_END__
```

```shell
$ nm libtest.a 

libtest.o:
0000000000000013 T add
0000000000000000 T func
                 U _GLOBAL_OFFSET_TABLE_
                 U puts
```

#### 参考

https://developer.aliyun.com/article/6371
https://www.cnblogs.com/wangliangblog/p/10766986.html
https://blog.csdn.net/BHJ1119/article/details/88219896
