---
data: 2026-03-13
---
![[Pasted image 20260313085613.png]]
192.168.1.11是点分十进制格式，本质是给人读的，是string，要先调用stoi（这里不是atoi，atoi转的是const char * ,然后再用htol从本机的小端口转成网络中用的大端口

- `inet_pton(int af , const char* sec , void * dst)`   ：p是ip，n代表net（网络），直接把点分十进制的ip地址转成网络字节序
	- af:代表当前ip协议是什么协议，有AF_INET、AF_INET6(ip)
- inet_ntop  