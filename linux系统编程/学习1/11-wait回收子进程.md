

# 详解 Linux `wait` 函数

## 一、基础介绍

`wait()` 是 Linux 系统调用，**父进程用来等待子进程退出、回收子进程资源，防止产生僵尸进程**。


```c
#include <sys/types.h>
#include <sys/wait.h>
```

函数原型：

```c
pid_t wait(int *wstatus);
```

---

## 二、参数 & 返回值

1. **传出参数 `wstatus`**
    
    - 整型指针，用于存放**子进程退出状态**。
    - 传 `NULL`：表示不关心子进程退出状态。
    
2. **返回值**
    
    - 成功：返回**已退出子进程的 PID**。
    - 失败：返回 `-1`（比如没有子进程）。
    

---

## 三、核心行为（重点）

1. **阻塞特性**
    
    调用 `wait()` 后，**父进程会立即阻塞**，直到任意一个子进程退出，函数才返回。
2. **功能**
    
    - 等待子进程结束
    - 回收子进程内核资源，**消灭僵尸进程**
    
3. 等价关系


```c
wait(&wstatus)  ==  waitpid(-1, &wstatus, 0);
```

---

## 四、退出状态解析宏（搭配 wait 使用）

`wstatus` 不能直接打印，要用系统提供的宏解析：

1. `WIFEXITED(wstatus)`
    
    判断子进程是否**正常退出**（return /exit），返回真 / 假。
2. `WEXITSTATUS(wstatus)`
    
    获取子进程**退出码**，仅在上一条宏为真时使用。
3. `WIFSIGNALED(wstatus)`
    
    判断子进程是否**被信号杀死**。
4. `WTERMSIG(wstatus)`
    
    获取杀死进程的**信号编号**。

---

## 五、最简示例代码

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h>

int main()
{
    pid_t pid = fork();

    if (pid == 0)
    {
        // 子进程
        printf("子进程运行，PID = %d\n", getpid());
        sleep(2);
        return 10;  // 子进程退出码 10
    }
    else if (pid > 0)
    {
        // 父进程
        int status;
        printf("父进程等待子进程...\n");
        pid_t ret = wait(&status);  // 阻塞等待子进程

        printf("收到子进程 PID = %d 退出\n", ret);
        if (WIFEXITED(status))
        {
	        // WEXITSTATUS(wstatus) -》 获取子进程退出码
            printf("子进程正常退出，退出码：%d\n", WEXITSTATUS(status));
        }
    }
    else
    {
        perror("fork");
    }
    return 0;
}
```

### 运行结果
![[Pasted image 20260529102441.png]]


### 运行逻辑

1. `fork` 创建子进程
2. 父进程执行 `wait` → **阻塞 2 秒**
3. 子进程休眠 2 秒后退出
4. 父进程被唤醒，解析并打印子进程退出状态

---

## 六、常见场景 & 注意点

1. **没有子进程时**
    
    `wait` 直接返回 `-1`，并设置错误。
2. **多个子进程**
    
    `wait` 一次只回收**最先退出**的一个子进程；多个子进程要用循环。
3. **僵尸进程本质**
    
    子进程退出后，PCB 仍保留在内核，父进程不调用 `wait/waitpid` 就会变成僵尸进程。
4. 和 `waitpid` 区别
    
    - `wait`：只能等**任意子进程**、只能阻塞、只能等进程终止。
    - `waitpid`：可指定等待某个子进程、支持非阻塞、可捕获暂停 / 恢复状态。
    

---

## 七、一句话总结

`wait()` 就是：**父进程停下等子进程结束，顺便收掉子进程残留资源**。