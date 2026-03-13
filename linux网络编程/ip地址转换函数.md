---
data: 2026-03-13
---
![[Pasted image 20260313085613.png]]
192.168.1.11本质是给人读的，是string，要先调用stoi（这里不是atoi，atoi转的是const char * ,然后再用htol从本机的小端口转成网络中用的大端口