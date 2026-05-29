

wnohang


# `waitpid()` 函数详解

`waitpid()` 是 Linux 系统中用于等待子进程状态变化的核心系统调用，是 `wait()` 的增强版，功能更灵活。

---

## 1. 函数原型

```c
#include <sys/types.h>
#include <sys/wait.h>

pid_t waitpid(pid_t pid, int *wstatus, int options);
```

---

## 2. 参数详解

### ① `pid_t pid`：指定要等待的子进程

表格

|`pid` 取值|含义|
|:--|:--|
|`> 0`|等待进程 ID 等于 `pid` 的**指定子进程**|
|`-1`|等待**任意**子进程（和 `wait()` 行为完全一致）|
|`0`|等待进程组 ID 与调用进程相同的任意子进程|
|`< -1`|等待进程组 ID 等于 `abs(pid)` 的任意子进程|

---

### ② `int *wstatus`：存储子进程状态信息

和 `wait()` 的 `status` 参数作用完全相同，是一个**输出型参数**：

- 传入 `int` 变量的地址，`waitpid()` 会把子进程的退出状态存入该变量
- 传入 `NULL`：表示不关心子进程的退出状态
- 必须用 `WIFEXITED`/`WEXITSTATUS` 等宏来解析这个状态值（你之前学过的宏）

---

### ③ `int options`：控制等待行为

常用选项是标志位的按位或组合：

表格

|选项|作用|
|:--|:--|
|`0`|阻塞等待：父进程会一直停在这里，直到子进程状态变化|
|`WNOHANG`|非阻塞等待：如果没有子进程退出，函数立即返回 `0`，父进程不会阻塞|
|`WUNTRACED`|同时等待 “子进程终止” 和 “子进程被信号暂停” 两种状态|
|`WCONTINUED`|等待被暂停的子进程收到 `SIGCONT` 信号恢复运行|

---

## 3. 返回值

表格

|返回值|含义|
|:--|:--|
|`> 0`|成功：返回已发生状态变化的子进程 PID|
|`0`|仅当 `options` 含 `WNOHANG` 时：没有子进程退出，立即返回 `0`|
|`-1`|失败：设置 `errno`（比如没有子进程、参数错误）|

---

## 4. 核心用法示例

### 示例 1：阻塞等待指定子进程（和你之前的代码对应）


```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h>

int main(void)
{
    pid_t pid = fork();
    int status;
    pid_t wpid;

    if (pid == 0) {
        // 子进程
        printf("子进程运行，PID=%d\n", getpid());
        sleep(2);
        exit(5); // 退出码设为5
    } else if (pid > 0) {
        // 父进程：阻塞等待 pid 指定的子进程
        wpid = waitpid(pid, &status, 0);

        if (wpid == -1) {
            perror("waitpid error");
            exit(1);
        }

        printf("等待到子进程 PID=%d\n", wpid);
        if (WIFEXITED(status)) {
            printf("子进程正常退出，退出码=%d\n", WEXITSTATUS(status));
        }
    }
    return 0;
}
```

### 示例 2：非阻塞等待（父进程不卡住）

```c



while (1) {
    // 非阻塞等待任意子进程
    wpid = waitpid(-1, &status, WNOHANG);
    if (wpid > 0) {
        printf("回收了子进程 PID=%d\n", wpid);
        break;
    } else if (wpid == 0) {
        // 没有子进程退出，父进程可以做其他事
        printf("没有子进程退出，父进程继续工作...\n");
        sleep(1);
    } else {
        perror("waitpid error");
        exit(1);
    }
}
```

---

## 5. 和 `wait()` 的核心区别

表格

|特性|`wait()`|`waitpid()`|
|:--|:--|:--|
|等待对象|只能等待任意子进程|可指定等待某个 / 某组子进程|
|阻塞行为|只能阻塞等待|支持非阻塞（`WNOHANG`）|
|状态监控|仅等待子进程终止|可监控终止、暂停、恢复等多种状态|
|等价关系|`wait(&status)`|`waitpid(-1, &status, 0)`|

---

如果你需要，我可以帮你写一个**结合 `WNOHANG` 非阻塞模式的完整示例**，让你直观看到父进程不被阻塞的效果。