# lab6 实验报告

## 练习0：填写已有实验

继续沿用之前的做法，用git diff结合Linux自带的patch命令来完成。 

	git diff <...> --relative=labcodes/lab5 | patch -d labcodes/lab6 -p1

## 练习1: 使用 Round Robin 调度算法（不需要编码）

**请理解并分析sched_calss中各个函数指针的用法，并接合Round Robin 调度算法描ucore的调度执行过程**

各个函数指针的作用如下：

1. `init`完成run_queue的初始化；
2. `enqueue`将一个PCB放入rq当中；
3. `dequeue`将一个PCB从rq当中移除；
4. `pick_next`选择下一个运行的进程，返回其PCB指针，并进行必要的信息更新；
5. `proc_tick`在时钟中断时调用，进行必要的信息更新。

ucore的调度执行过程与之前并没有发生太大的变化，只是将调度算法独立出来了而已。

**请在实验报告中简要说明如何设计实现”多级反馈队列调度算法“，给出概要设计，鼓励给出详细设计**

将`struct run_queue`当中一个链表改为多个，并在`proc_tick`函数当中进行优先级调整（当我们在这个函数里标记一个进程`need_resched`，就说明时间片用尽，可以根据具体策略将其降级）。

## 练习2: 实现 Stride Scheduling 调度算法（需要编码）

**请在实验报告中简要说明你的设计实现过程。**

由于斜堆的实现已经给出，调度算法的实现变得非常显然。

唯一需要一点考虑的地方是常数`BIG_STRIDE`的设置。实验指导书当中给出了一个结论：

> STRIDE_MAX – STRIDE_MIN <= BIG_STRIDE

注意到我们希望通过STRIDE_A - STRIDE_B的正负号来判断实际的优先级大小，这要求实际的优先级的差不能大于有符号32位整数的能表示的最大整数。因此我们取BIG_STRIDE为有符号32位整数的上界，2^31 - 1，就能满足要求。