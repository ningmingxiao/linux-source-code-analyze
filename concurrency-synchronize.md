## 并发同步

`并发` 是指在某一时间段内能够处理多个任务的能力，而 `并行` 是指同一时间能够处理多个任务的能力。并发和并行看起来很像，但实际上是有区别的，如下图（图片来源于网络）：

![concurrency-parallelism](https://raw.githubusercontent.com/liexusong/linux-source-code-analyze/master/images/concurrency-synchronize-1.png)

上图的意思是，有两条在排队买咖啡的队列，并发只有一架咖啡机在处理，而并行就有两架的咖啡机在处理。咖啡机的数量越多，并行能力就越强。

可以把上面的两条队列看成两个进程，并发就是指只有单个CPU在处理，而并行就有两个CPU在处理。为了让两个进程在单核CPU中也能得到执行，一般的做法就是让每个进程交替执行一段时间，比如让每个进程固定执行 `100毫秒`，执行时间使用完后切换到其他进程执行。而并行就没有这种问题，因为有两个CPU，所以两个进程可以同时执行。如下图：

![concurrency-parallelism](https://raw.githubusercontent.com/liexusong/linux-source-code-analyze/master/images/concurrency-synchronize-2.png)

### 原子操作

上面介绍过，并发有可能会打断当前执行的进程，然后替切换成其他进程执行。如果有两个进程同时对一个共享变量 `count` 进行加一操作，由于C语言的 `count++` 操作会被翻译成如下指令：
```asm
mov eax, [count]
inc eax
mov [count], eax
```
那么在并发的情况下，有可能出现如下问题：

![concurrency-problem](https://raw.githubusercontent.com/liexusong/linux-source-code-analyze/master/images/concurrency-synchronize-3.jpg)
