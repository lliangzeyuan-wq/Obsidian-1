---
data: 2026-03-16
---
```cpp vim
#include <stdio.h>
#include <ctype.h>
#include <sys/socket.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <errno.h>
#include <pthread.h>
#include <arpa/inet.h>  // 补充缺失的头文件，用于 htons/htonl/INADDR_ANY 等

#define SERV_PORT 9527
#define MY_BUFSIZ 1024     // 补充代码中使用的 BUFSIZ 宏定义

void sys_err(const char *str)
{
    perror(str);
    exit(1);
}

int main(int argc, char *argv[])
{
    int lfd = 0, cfd = 0;
    int ret, i;  // 补充循环变量 i 的声明
    char buf[MY_BUFSIZ],client_IP[1024];

    struct sockaddr_in serv_addr, clit_addr;
    socklen_t clit_addr_len;

    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(SERV_PORT);
    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);

    lfd = socket(AF_INET, SOCK_STREAM, 0);
    if (lfd == -1) {
        sys_err("socket error");
    }

    bind(lfd, (struct sockaddr *)&serv_addr, sizeof(serv_addr));

    listen(lfd, 128);

    clit_addr_len = sizeof(clit_addr);

    cfd = accept(lfd, (struct sockaddr *)&clit_addr, &clit_addr_len);
    if (cfd == -1){
        sys_err("accept error");
	}
	printf("client ip:%s port:%d\n",inet_ntop(AF_INET,&clit_addr.sin_addr.s_addr,client_IP,sizeof(client_IP)),ntohs(clit_addr..sin_port));
	while(1)
	{
		ret=read(cfd,buf,sizeof(buf));
		write(STDOUT_FILENO,buf,ret);
		for(i=0;i<ret;++i)
		{
			buf[i]=toupper(buf[i]);
		}
		write(cfd,buf,ret);
	}
    close(lfd);  // 补充关闭监听套接字
    close(cfd);  // 补充关闭客户端套接字

    return 0;
}
```