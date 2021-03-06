# 任务服务

## 概念

任务（task）是一个可以被抢占的执行的线程，该执行线程是应用处理过程实现的一部分。它一般被用于处理那些对于线（fiber）或中断服务（ISR）来讲太长或太复杂的任务。

微内核应用只能定义单一的应用任务，也就是后台任务（background task），当没有线或者中断服务需要运行时，便会运行该任务。

> Note：后台任务与在微内核中使用的任务是完全不同的，更对有关这方面的信息请查看[微内核任务服务](https://www.zephyrproject.org/doc/kernel/microkernel/microkernel_tasks.html#microkernel-tasks)。

### 任务生命周期

在系统初始化时，内核会自动启动后台任务。

一旦后台任务被启动，它将会永远的运行。如果该任务尝试通过从`main()`中返回来终止任务，由于后台任务必须总是被执行，内核会将该任务置为永久空闲的状态。

### 任务调度

由于线或中断服务的执行具有优先权，纳内核的调度器仅会在没有任何线或中断服务需要执行时才会调度后台任务来执行。

当后台任务切换至线或终端服务时，内核会自动保存后台任务的CPU寄存器值。之后，当后台任务再次需要恢复执行时，内核会恢复其CPU寄存器的值。

## 使用方法

定义一个背景任务。

应用必须提供一个具有如下形式的函数：

```
void main(void)
{
    /* background task processing */
    ...
    /* (optional) enter permanent idling state */
    return;
}
```

该函数会被用作后台任务的入口函数。如果纳内核应用不需要执行任何任务级的处理，`main()`函数只需简单的返回即可。

`MAIN_STACK_SIZE`配置选项指定了被后台任务栈以及其他执行上线文信息所使用的内存区域大小（单位为字节）。

## API

纳内核提供了如下的API函数用来维护后台任务。

`task_sleep()`

将后台任务休眠一段指定的时间。


