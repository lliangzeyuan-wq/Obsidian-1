

## 硬件异常产生信号

- 除0操作  -》   8)SIGFPE (浮点数除外)   **FPE** = **Floating Point Exception**      **浮点异常**  
- 非法访问内存 → 11) SIGSEGV (段错误)
- 总线错误 → 7) SIGBUS

# kill函数和kill命令

## 一、kill 函数（可复制）

c

运行

```
#include <sys/types.h>
#include <signal.h>

int kill(pid_t pid, int sig);
```

- 功能：向进程 / 进程组**发送信号**（不只是杀死）
- 返回值：成功 `0`；失败 `-1`，并设置 `errno`

### pid 取值含义

- `pid > 0`：发给**指定进程**
- `pid = 0`：发给**同进程组所有进程**
- `pid < -1`：发给**进程组 |pid|**
- `pid = -1`：发给**所送给进程有权限发送的系统中所有进程**

进程组：每个进程都属于一个进程组，进程组是一个或多个进程集合，他们相互关联，共同完成一个实体任务，每个进程组都有一个进程组长，默认进程组 ID 与进程组长 ID 相同。(当父进程创建一个子进程的时候，父子进程默认在同一个进程组）

权限保护：super 用户 (root) 可以发送信号给任意用户，普通用户是不能向系统用户发送信号的。 kill -9 (root 用户的 pid) 是不可以的。同样，普通用户也不能向其他普通用户发送信号，终止其进程。 只能向自己创建的进程发送信号。普通用户==**基本规则是：发送者实际或有效用户 ID == 接收者实际或有效用户 ID。**==


练习：循环创建 5 个子进程，父进程用 kill 函数终止任一子进程。

## 二、示例：循环创建 5 个子进程，父进程杀死其中一个（可直接编译运行）

c

运行

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>

int main()
{
    pid_t pids[5];
    int i;

    /* 循环创建5个子进程 */
    for (i = 0; i < 5; i++)
    {
        pids[i] = fork();
        if (pids[i] == -1)
        {
            perror("fork");
            exit(1);
        }
        if (pids[i] == 0)
        {
            /* 子进程：循环打印 */
            while (1)
            {
                printf("child %d (pid=%d) running\n", i, getpid());
                sleep(1);
            }
            exit(0);
        }
    }

    /* 父进程先等3秒 */
    sleep(3);
    printf("parent: kill child 2 (pid=%d)\n", pids[2]);

    /* 杀死第3个子进程（下标2） */
    if (kill(pids[2], SIGKILL) == -1)
    {
        perror("kill");
    }

    /* 回收僵尸子进程 */
    wait(NULL);

    sleep(5);
    printf("parent exit\n");
    return 0;
}
```