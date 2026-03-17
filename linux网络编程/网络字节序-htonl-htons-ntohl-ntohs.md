---
data: 2026-03-13
---
- 小端法（pc本地存储）：高位存高地址，低位存低地址。
 eg：int a=0x12345678
 ![[Pasted image 20260313084112.png]]
 - 大端法（网络存储）：高位存低地址，低位存高地址。
 - 因此在本机和网络间进行传输的时候，要进行转换

- hs代表host（本机），to  ，n代表net（网络），l代表4字节32位（存ip），s代表short（2字节16位，存端口(port)）
 htonl
 htons
 ntohl
 ntohs
- ==将本机的int型的ip/port和网络中的ip/port互换==