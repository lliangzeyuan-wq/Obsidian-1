---
data: 2026-03-13
---
![[Pasted image 20260313085613.png]]
192.168.1.11是点分十进制格式，本质是给人读的，是string/const char* ，要先调用stoi/atoi ,然后再用htol从本机的小端口转成网络中用的大端口


- `int inet_pton(int af , const char* src , void * dst)`   ：p是ip，n代表net（网络），直接==把点分十进制的ip地址转成网络字节序==
	- af:代表当前ip协议是什么协议，有AF_INET、AF_INET6(ipv4和ipv6)
	- src ：传入，ip地址（点分十进制）
	- dst：传出，转换后的网络字节序IP地址（dst是void* 类型（泛型指针））
	- 返回值：成功（1），异常（0，说明src指向的不是一个有效的ip地址），失败（-1）
- `const char * inet_ntop(int af,const void *src,char *dst,socklen_t size)`  ==把网络字节序转换成点分十进制的ip地址==
	- af:代表当前ip协议是什么协议，有AF_INET、AF_INET6(ipv4和ipv6)
	- src ：传入，网络字节序IP地址
	- dst：传出，本地字节序（string ip）
	- size：dst的大小
	- 返回值：成功：dst，失败：NULL