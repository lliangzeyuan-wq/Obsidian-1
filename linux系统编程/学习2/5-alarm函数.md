
# alarm 知识点

alarm 函数

设置定时器 (闹钟)。在指定 seconds 后，内核会给当前进程发送 14 号 SIGALRM 信号。进程收到该信号，默认动作终止。

==每个进程都有且只有唯一一个定时器。==

`unsigned int alarm(unsigned int seconds);` 返回 0 或==剩余的秒数==，无失败。

常用：取消定时器 `alarm(0)`，返回旧闹钟余下秒数。

示例：`alarm(5) → 3sec → alarm(4) → 5sec → alarm(5) → alarm(   0)`

定时，与进程状态无关 (自然定时法)! 就绪、运行、挂起 (阻塞、暂停)、终止、僵尸… 无论进程处于何种状态，alarm 都计时。

使用 time 命令查看程序执行的时间。程序运行的瓶颈在于 IO，优化程序，首选优化 IO。

实际执行时间 = 系统时间 + 用户时间 + 等待时间



练习：编写程序，测试你使用的计算机 1 秒钟能数多少个数。【alarm.c】

```cpp
#include <stdio.h>
#include <unistd.h>

int main(void)
{
    int i;
    alarm(1);
    for(i = 0 ; ; i++)
        printf("%d\n", i);
    return 0;
}
```

### **1 秒后程序结束**

1. `alarm(1)`：1 秒后内核发送 **SIGALRM (14 号信号)**
2. 进程收到 SIGALRM**默认处理：直接终止程序**
3. `for(;;)`死循环不停计数打印，1 秒到收到信号，程序立刻退出

- `time ./alarm`  结果：
![[Pasted image 20260602132133.png]]
## 1. real = 墙上实际耗时（1.002 秒）

**物理挂钟时间，从程序启动 → alarm (1) 发信号退出，一共跑了 1.002s**

刚好符合 `alarm(1)` 设定 1 秒终止程序，多出来 0.002 是系统调度损耗。

## 2. user = 用户态 CPU 耗时（0.201s）

CPU 在**应用代码**上运算的时间：也就是你`for循环 i++、printf`这些 C 代码占用 CPU 的时长。

## 3. sys = 内核态 CPU 耗时（0.753s）

CPU 在内核系统调用的耗时：`printf`要调用系统 IO、内核写终端，大量`printf`频繁触发系统调用，所以 sys 数值很大。

实际执行时间(real) = 系统时间(sys) + 用户时间(user) + 等待时间(real-sys-user)

### 关键规律

`user + sys = 0.954s`（CPU 真正干活总时长）

`real(1.002) > user+sys`：剩下的时间是进程**等待 IO 输出到屏幕**的阻塞空闲时间。

### 结合你的代码结论

你的代码里`printf`疯狂打印，**IO 阻塞占用大量时间**，CPU 空闲等输出，所以 sys 很高、real 略大于 1 秒。

> 注释掉`printf("%d\n",i);`只做 i++，sys 会暴跌，计数数字暴涨。