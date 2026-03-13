---
data: 2026-03-13
---
### socket函数
- 作用：创建一个套接字
- 头文件： `#include<sys/socket.h>`
- 函数原型：` int socket(int domain, int type, int protocol);`
    - domain：AF_INET、AF_INET6（ipv4，ipv6）、AF_UNIX 
	- type :  所选用的 SOCK_STREAM、SOCK_DGRAM    