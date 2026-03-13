---
data: 2026-03-13
---

![[Pasted image 20260313101847.png]]
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
bind( fd,(struct sockaddr*)&addr,size);
```


![[Pasted image 20260313101847.png]]
![[Pasted image 20260313101905.png]]

