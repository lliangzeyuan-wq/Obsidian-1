---
data: 2026-03-13
---

![[Pasted image 20260313101847.png]]
- sin_family :   AF_INET/AF_INET6(ipv4 , ipv6)
- sin_port : 网络字节序的端口号（port），用的时候htos把来初始化一下
- sin_addr：网络地址，inet_pton来初始化(他这里面又套了一个结构体，你初始化的时候就  (sockaddr_in).sin_addr.s_addr( xxx )   来初始化就行了       )
![[Pasted image 20260313101905.png]]



![[Pasted image 20260313105357.png]]
- sockaddr_in   这里的in是internet的意思
- sockaddr_in 的字节大小和sockaddr是一样的，只是内部更加细分了一下。然后sockaddr就被废弃了，但是现在一些函数由于历史遗留问题，传参的时候还是要把sockaddr_in强转成sockaddr
### bind函数
#### 函数原型
- `、int bind(int sockfd,const struct sockaddr *addr,socklen_t addlen);`
	- sockfd：文件描述符


### 实际应用
```cpp
struct sockaddr_in addr;
addr.sin_family = AF_INET/AF_INET6;
addr.sin_port = htons(9526);
int dst;
inet_pton(AF_INET,"199.157.22.45",(void*)&dst);
addr.sin_addr.s_addr = dst;
bind( fd,(struct sockaddr*)&addr,size);
```


![[Pasted image 20260313101847.png]]
![[Pasted image 20260313101905.png]]

- 在这里贴一个，复习适用
`int inet_pton(int af , const char* src , void * dst)`   ：p是ip，n代表net（网络），直接==把点分十进制的ip地址转成网络字节序==