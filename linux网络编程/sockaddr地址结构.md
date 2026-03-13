---
data: 2026-03-13
---
   `struct sockaddr_in addr;`
`bind(fd,(struct sockaddr *)&addr,size)`

![[Pasted image 20260313101847.png]]
![[Pasted image 20260313101905.png]]


### bind函数
#### 函数原型
- `、int bind(int sockfd,const struct sockaddr *addr,socklen_t addlen);`
	- sockfd：文件描述符
	- 