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
	addr.sin_family = AF_INET;  这里的ip类型必须和socket函数里的domain保持一致
	addr.sin_port = htons(9526);   //端口号
	addr.sin_addr.s_addr=htonl(INADDR_ANY);  //ip地址
	- addr :  (struct sockaddr*)&addr  ，  是传入参数
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
- 作用：阻塞等待客户端建立连接，成功的话，返回一个与客户端成功连接的socket文件描述符
- 头文件：和socket函数，bind, listen函数一样， `#include<sys/socket.h>`
- 函数原型：`int accept(int sockfd,struct sockaddr *addr,socklent_t *addrlen)`
	- sockfd  :  socket函数返回值
	- addr：传出参数。传入一个空值，返回客户端的addr（包含地址结构：ip+port）
	- addrlen: 传入传出参数，用的是指针（其他地方的长度用的都是值）。你要告诉内核addr结构体的最大长度，内核会修改成客户端地址实际占用的长度
	- 返回值：能与客户端进行数据通讯的新的socket对应的文件描述符


### 客户端connect函数
- 作用：使用现有的socket与服务器建立连接
- 头文件：常客，`#include<sys/socket.h>`
- 函数原型：` int connect(int sockfd, const struct sockaddr *addr,socklen_t addrlen);`
	- sockfd：sockfd 函数返回值
	- addr：传入参数，要连接的==服务器==的addr
	- addrlen：传入参数，==服务器地址addr的大小==
	- 返回值：成功：0  ， 失败： -1 errno


如果不适用bind绑定客户端的地址结构（ip+port），采用“隐式绑定”





---
# read 函数

- **作用**：从指定的文件描述符（socket）中读取数据
    
- **头文件**：`#include <unistd.h>`
    
- **函数原型**：`ssize_t read(int fd, void *buf, size_t count);`
    
    - `fd`：如果是服务端：`accept()` 函数的返回值（与客户端通信的文件描述符）；如果是客户端：`socket()`函数的返回值
    - `buf`：用于存储读取数据的缓冲区（字符数组）
    - `count`：期望读取的最大字节数
    
- **返回值**：
    
    - 成功：**> 0**，实际读取到的字节数
    - 对端关闭连接：**0**，表示读到 EOF（文件结束符）
    - 失败：**-1**，错误信息保存在`errno`中
    

---

# write 函数

- **作用**：向指定的文件描述符（socket）中写入数据
    
- **头文件**：`#include <unistd.h>`
    
- **函数原型**：`ssize_t write(int fd, const void *buf, size_t count);`
    
    - `fd`：`accept()` 函数的返回值（与客户端通信的文件描述符）
    - `buf`：要发送的数据所在的缓冲区
    - `count`：期望写入的字节数

在 Linux 系统里：
- **0 = STDIN_FILENO**（标准输入）  -》键盘
- **1 = STDOUT_FILENO**（标准输出）-》终端屏幕
- **2 = STDERR_FILENO**（标准错误）-》终端屏幕
这3个宏定义饱包含在头文件`#include<unistd.h>`中
- 应用：
`wtite(STDOUT_FILENO,buf,sizeof(buf))`   把buf中的内容输出在屏幕上