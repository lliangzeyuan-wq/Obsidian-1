
先把最关键的结论放在前面：

- **getpid()**：拿到**自己**的进程号（PID）
- **getppid()**：拿到**父进程**的进程号（PPID）

下面拆开讲。

---

## 1. 头文件与函数原型

在 Linux 下用这两个函数，需要：

c

运行

```
#include <unistd.h>   // 必须
#include <sys/types.h>// pid_t 类型定义
```

原型：

c

运行

```
pid_t getpid(void);
pid_t getppid(void);
```

- **pid_t**：进程号类型，本质是**有符号整型**（为了能表示 -1）
- **无参数**，调用就返回对应 ID
- **永远成功**，不会返回错误

---

## 2. getpid ()：获取自己的 PID

- **作用**：返回**当前正在运行的进程**的 ID
- **通俗说**：“我是谁？我的身份证号是多少？”

示例：

c

运行

```
printf("我的进程ID = %d\n", getpid());
```

---

## 3. getppid ()：获取父进程的 PID

- **作用**：返回**创建自己的那个进程**的 ID（父进程）
- **ppid = parent pid**
- **通俗说**：“谁生了我？我爸爸的身份证号是多少？”

示例：

c

运行

```
printf("我的父进程ID = %d\n", getppid());
```

---

## 4. 和 fork () 配合看（最常见用法）

c

运行

```
pid_t pid = fork();

if (pid > 0) {
    // 父进程
    printf("父进程：我自己 PID=%d，我的孩子 PID=%d\n",
           getpid(), pid);
}
else if (pid == 0) {
    // 子进程
    printf("子进程：我自己 PID=%d，我的父进程 PID=%d\n",
           getpid(), getppid());
}
```

典型输出：

plaintext

```
父进程：我自己 PID=8309，我的孩子 PID=8310
子进程：我自己 PID=8310，我的父进程 PID=8309
```

---

## 5. 你之前看到的 7876 怎么来的？

层级关系：

plaintext

```
bash（7876）
 └── ./fork（8309）         ← 你运行的程序
      └── 子进程（8310）
```

- `./fork` 里：
    
    - `getpid()` → **8309**（自己）
    - `getppid()` → **7876**（父进程是 bash）
    

所以：

- **7876 = bash 的 PID**
- 你在终端敲命令，所有程序的父进程都是 **bash**

---

## 6. 一句话总结

- **getpid()**：获取**自己**的进程号
- **getppid()**：获取**父进程**的进程号


### 示例程序
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>

int main(int argc , char * argv[])
{
	printf("before fork-1-\n");
	printf("before fork-2-\n");
	printf("before fork-3-\n");
	printf("before fork-4-\n");

	pid_t pid = fork();
	if(pid == -1)
	{
		perror("fork error");
		exit(1);
	}
	else if(pid == 0)
	{
		printf("---child is created , pid = %d , parent-pid = %d\n", getpid(), getppid());
	}
	else if(pid > 0)
	{
		printf("---parent process : my child is %d , my pid:%d , my parent pid:%d\n" , pid
				,getpid() , getppid());
	}

	printf("=================end of file\n");
	
	return 0;
	
}
```


### 运行结果
![[Pasted image 20260528154336.png]]


### 2233是怎么来的
![[Pasted image 20260528154423.png]]
可以看到，2233 对应的就是bash , 即终端。因为运行这个程序的就是终端，由此可知，所有的程序的父进程都是终端