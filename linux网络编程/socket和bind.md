---
data: 2026-03-13
---
### socket函数
- 作用：创建一个套接字
- 头文件： `#include<sys/socket.h>`
- 函数原型：` int socket(int domain, int type, int protocol);`
    - domain：AF_INET、AF_INET6（ipv4，ipv6）、AF_UNIX 
	- type :  所选用的数据传输协议： SOCK_STREAM（流氏协议）、SOCK_DGRAM   （报氏协议）
	- protocol : 你所选用的数据传输协议中的代表协议： 0 (根据type中所选的协议选择代表协议，流氏协议中的代表协议氏tcp，报氏协议中的代表协议是udp)
	- 返回值：成功：新套接字所对应的文件描述符。失败：-1 errno