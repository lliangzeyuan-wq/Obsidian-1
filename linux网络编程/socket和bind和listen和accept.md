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
	- 实战：`fd = socket(AF_INET,SOCK_STREAM,0);`
	![[Pasted image 20260313150351.png]]

### bind函数
- 作用：给socket绑定一个地址结构（ip+port）
- 头文件：和socket函数一样， `#include<sys/socket.h>`
- 函数原型 : `int bind(int sockfd, const struct sockaddr *addr,socklen_t addrlen);`
	- sockfd ：socket函数的返回值
	
	struct sockaddr_in addr;
	addr.sin_family = AF_INET;
	addr.sin_addr.s_addr=htonl(INADDR_ANY);
	- addr :  (struct sockaddr*)&addr
	- addrlen : sizeof(addr)    地址结构的大小
	- 返回值：成功：0    失败：-1   errno


### listen函数
- 作用：设置监听上限（即同时和服务端连接的客户端的数量的最大上限）
- 头文件：和socket函数，bind函数一样， `#include<sys/socket.h>`
- 函数原型：`int listen(int sockfd, int backlog);`
	- sockfd : socket函数返回值
	- backlog：上限数值。最大值128
	- 返回值：成功：0     失败：-1 errno

### accept函数
- 头文件：和socket函数，bind, listen函数一样， `#include<sys/socket.h>`