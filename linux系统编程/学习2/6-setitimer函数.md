# setitimer 函数 学习笔记

## 一、函数头文件与原型

```c
#include <sys/time.h>
int setitimer(int which, const struct itimerval *new_val, struct itimerval *old_val);
```

- 返回值：成功`0`，失败`-1`，`perror`查看错误

## 二、参数 which：三种计时模式（对应不同信号）

表格

|参数|信号|计时规则|
|---|---|---|
|ITIMER_REAL|SIGALRM(14)|**自然墙上时间**，进程休眠 / 阻塞照样计时（对标 alarm）|
|ITIMER_VIRTUAL|SIGVTALRM(26)|只统计**用户态 CPU 占用时间**，休眠不计时|
|ITIMER_PROF|SIGPROF(27)|用户 + 内核 CPU 总耗时（用户代码 + 系统调用）|

> 常用：`ITIMER_REAL`，和 alarm 共用 SIGALRM 信号。

## 三、核心结构体：struct itimerval

c

运行

```
struct itimerval {
    struct timeval it_interval; // 循环周期：第一次超时后，后续定时时长
    struct timeval it_value;    // 首次定时：从setitimer调用开始第一次倒计时
};

struct timeval {
    time_t      tv_sec;     // 秒
    suseconds_t tv_usec;    // 微秒(1s=1000000us，范围0~999999)
};
```

### 字段口诀

`it_value`：**第一次多久触发信号**

`it_interval`：**首次触发后，后续每隔多久循环触发**

1. `it_interval={0,0}`：**单次定时，只触发 1 次（等价 alarm）**
2. `it_interval≠{0,0}`：**周期性循环定时，反复发信号**

# 四
- 函数原型
```c
#include <sys/time.h>
int setitimer(int which, const struct itimerval *new_val, struct itimerval *old_val);
```

- 第二个参数作用：设置这个闹钟的时间
- 第三个参数作用：传出参数，把上一个闹钟的的定时参数

## 四、完整周期示例代码（可直接编译运行）

运行

```c
#include <stdio.h>
#include <sys/time.h>
#include <signal.h>

// SIGALRM信号处理函数
void myfunc(int signo)
{
    printf("hello world\n");
}

int main(void)
{
    struct itimerval it, oldit;
    // 绑定SIGALRM触发后的回调函数
    signal(SIGALRM, myfunc);

    // 首次2秒触发
    it.it_value.tv_sec = 2;
    it.it_value.tv_usec = 0;
    // 之后每隔5秒循环触发
    it.it_interval.tv_sec = 5;
    it.it_interval.tv_usec = 0;

    // 开启自然定时闹钟
    if(setitimer(ITIMER_REAL, &it, &oldit) == -1){
        perror("setitimer error");
        return -1;
    }

    while(1); // 死循环阻塞，进程不退出，等待定时信号
    return 0;
}
```

### 程序运行时序

- 0s：启动定时器
- 2s：第一次打印`hello world`
- 7s：第二次打印（2+5）
- 12s：第三次打印……**无限每 5s 循环输出**

## 五、setitimer vs alarm

1. alarm：**单一定时、只能整秒、一个进程仅 1 个闹钟**，只能单次触发
2. setitimer：**微秒精度、3 类独立闹钟、支持周期性循环定时**，完全替代 alarm