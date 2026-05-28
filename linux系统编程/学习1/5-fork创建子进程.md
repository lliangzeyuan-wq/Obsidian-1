
fork 函数

创建一个子进程。

pid_t fork (void);

失败返回 - 1；

成功返回：

① 父进程返回子进程的 ID (非负)

② 子进程返回 0

pid_t 类型表示进程 ID，但为了表示 - 1，它是有符号整型。

(0 不是有效进程 ID，init 进程的 PID 最小，为 1)

注意返回值：不是 fork 函数能返回两个值，而是 fork 之后，相当于 fork 函数变为了两个，父子进程需【各自】返回一个。

![[Pasted image 20260528150813.png]]



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
		printf("---child is created\n");
	}
	else if(pid > 0)
	{
		printf("---parent process : my child is %d\n" , pid);
	}

	printf("=================end of file\n");
	
	return 0;
	
}
```

### 程序运行结果
- 从这个程序可以看出，从 fork（）函数之后，程序就分叉了（之前是共有的），分成了两个部分，一个是父进程，一个是子进程。

![[Pasted image 20260528150930.png]]