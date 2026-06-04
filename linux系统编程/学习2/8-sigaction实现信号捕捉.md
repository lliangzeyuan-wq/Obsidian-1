
# sigaction 函数全解（Linux 标准信号注册，替代 signal）

## 一、函数原型

c

运行

```
int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
```

- **返回值**：成功返回`0`，失败返回`-1`并置 errno
- 参数说明：
    
    1. `signum`：要操作的**信号编号**（如 SIGINT=2）
    2. `act`：入参，**新的信号处理配置**，填 NULL 代表不修改当前处理方式
    3. `oldact`：出参，保存**原来旧的信号处理配置**，不需要则填 NULL
    

> 核心用途：**POSIX 标准注册信号捕捉，跨 Linux/Unix 行为统一，是工程替代 signal 的首选**

## 二、struct sigaction 结构体（关键）

c

运行

```
struct sigaction {
    // 1. 简易处理函数（和signal的handler一模一样，二选一）
    void (*sa_handler)(int);
    
    
    // 2. （一般不用），扩展处理函数（能拿到信号详细信息，开启SA_SIGINFO才生效）
    void (*sa_sigaction)(int, siginfo_t *, void *);
    
    
    // 3. 信号屏蔽集：执行回调函数期间，临时屏蔽的信号
    sigset_t sa_mask;
    // 4. 标志位，控制函数行为
    int sa_flags;
};
```

### 成员拆分

1. **sa_handler**
    
    和之前`signal`的回调一致：`void func(int sig)`，赋值`SIG_IGN`忽略信号、`SIG_DFL`恢复默认。

> sa_handler 和 sa_sigaction **两个函数指针互斥，只用一个**

2. **sa_sigaction**
    
    带详细信息的回调，第二个参数`siginfo_t`结构体里存发送信号的进程 PID、信号原因，`sa_flags`设`SA_SIGINFO`才启用这个函数。
    
3. **sa_mask（高频考点）**
    
    **进程正在执行当前信号的回调函数时，自动屏蔽 sa_mask 里写的所有信号**，回调结束自动解除屏蔽。
    
    例：SIGINT 回调运行中，临时屏蔽 SIGQUIT，避免嵌套打断。
    
4. **sa_flags 常用宏**
    

- `SA_SIGINFO`：启用 sa_sigaction 扩展回调
- `SA_RESTART`：被信号打断的系统调用 (read/write) 自动重启（不会返回 EINTR 错误）
- `SA_RESETHAND`：信号触发一次后，自动变回默认处理（模仿旧版 signal 特性）

## 三、最简示例（替换 signal 捕捉 Ctrl+C）

c

运行

```
#include <stdio.h>
#include <signal.h>

void sig_cb(int sig)
{
    printf("收到SIGINT：%d\n",sig);
}

int main(void)
{
    struct sigaction act;
    // 绑定回调函数
    act.sa_handler = sig_cb;
    // 清空临时屏蔽集
    sigemptyset(&act.sa_mask);
    // 默认标志
    act.sa_flags = 0;

    // 注册SIGINT信号
    sigaction(SIGINT,&act,NULL);

    while(1); // 循环等待信号
    return 0;
}
```

运行：`gcc xxx.c && ./a.out`，按`Ctrl+C`触发回调，程序不退出。

## 四、对比 signal 优势

1. `signal`：不同系统实现不一致，部分系统捕获一次信号后自动变回默认处理；
2. `sigaction`：POSIX 规范，**一次注册永久生效**，可自定义屏蔽集、获取信号来源信息、控制系统调用重启，企业开发必用。

## 补充：和之前未决信号 sigpending 配套

sigaction 的`sa_mask`配合`sigprocmask`阻塞信号，被阻塞的信号就会变成**未决信号**，通过`sigpending`获取，正好对应你文档开头的练习题。   