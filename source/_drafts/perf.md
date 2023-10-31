---
title: perf
date: 2023-12-09 13:11:29
abstracts: performance
tags:
  - perf
  - flamegraph
categories:
  - linux
cover: ''
feature: false
mathjax: false
---

### 线程状态

<div align="center">
  <img src="/img/thread_states.png" style="width:50%;height:50%;">
</div>

* On-CPU 线程在 CPU 上运行
* Off-CPU 线程由于等待 IO、锁、定时器、换页等原因阻塞

### 调度延迟

一个任务具备运行条件，进入 run queue，到真正执行的时间，包括
* 调度器决策时间
* 任务唤醒与实际运行之间的延迟，即上下文切换时间

### perf

#### 工具安装

```shell
$ sudo apt install linux-tools-common
$ sudo apt install linux-tools-`uname -a | awk '{print $3}'`
$ sudo apt install linux-cloud-tools-`uname -a | awk '{print $3}'`
```

#### 工具使用

##### 调用栈

示例程序

<details>
<summary>c++</summary>

```c++
$ cat p.cc 
#include <stdio.h>
#include <signal.h>

#include <atomic>
#include <thread>

class Perf;
std::atomic<bool> shutdown = {false};

void sighandle(int sig) {
  (void)sig;
  printf("\n shutdown \n");
  shutdown.exchange(true);
}

void foo() {
  while (!shutdown) {
    std::this_thread::sleep_for(std::chrono::milliseconds(1));
  }
}

void bar() {
  foo();
}

class Perf {
  public:
    void f() {
      bar();
    }
};

void func() {
  while (!shutdown) {
    Perf p;
    p.f();
  }
}

int main() {
  signal(SIGINT, sighandle);
  std::thread t = std::thread(func);
  t.join();
  signal(SIGINT, SIG_DFL);
  return 0;
}
```
</details>

```shell
$ sudo perf record -F 999 -p <PID> -g -- sleep 10
[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 0.077 MB perf.data (403 samples) ]
$ sudo perf script -i perf.data &> perf.unfold
$ FlameGraph/stackcollapse-perf.pl perf.unfold &> perf.folded
$ git clone https://github.com/brendangregg/FlameGraph.git
$ FlameGraph/flamegraph.pl perf.folded &> flamegraph.svg
```

`record` 数据抓取
`-F` 采样频率
`-p` 可抓取指定进程
`-g` 记录调用栈
`-- sleep 10` 抓取时间 10s
`script` 数据解析
`stackcollapse-perf.pl` 折叠调用栈
`flamegraph.pl` 生成火焰图

<div align="center">
  <img src="/img/flamegraph.svg">
  火焰图
</div>

火焰图中，每个格子代表一个函数，每一列代表一个调用栈，按调用关系从下往上排列，采样时最上方的函数正在占用 CPU

格子的宽度代表函数在采样中出现的频率，宽度越大，函数是性能瓶颈的可能性越大

从火焰图中可以找到程序的调用栈

<div align="center">
  <img src="/img/callstack.png" style="width:30%;height:30%;">
  调用栈
</div>

##### 事件

* sched_switch 任务切换事件

* sched_stat_sleep 睡眠事件

* sched_waking

* sched_wakeup

* page-faults

`R` 任务在时间片结束前处于运行状态
`R+` 任务在时间片结束前由于抢占失去 CPU 使用权
`S` 任务在时间片结束前进入睡眠状态

##### 调度延迟

```shell

$ perf sched latency --sort max -i perf.data
```






### 参考

https://www.brendangregg.com/offcpuanalysis.html
https://www.brendangregg.com/FlameGraphs/offcpuflamegraphs.html
https://github.com/brendangregg/FlameGraph
