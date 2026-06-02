
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

