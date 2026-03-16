---
data: 2026-03-14
---
命名：只能叫makefile  或    Makefile

基本原则：
1.若想生成目标，检查规则中的依赖条件是否存在，若不存在，则寻找是否有规则用来生成该依赖文件（倒着写）
2.检查规则中的目标是否需要更新，必须先检查它的所有依赖，依赖中有任一被更新，则目标必须更新（make的时候会只能的检测更新）
![[Pasted image 20260314184802.png]]
所以，像这里，你要倒着写，让系统有东西可寻找


![[Pasted image 20260314181559.png]]
- 一个规则
		目标：依赖条件
			（一个tab缩进）命令
	- 1.目标的时间必须晚于依赖条件的时间 ，否则更新目录。（make的时候会只能的检测更新）
	- 2.依赖条件如果不存在，找寻新的规则去产生依赖（倒着写）
- ALL : 指定makefile的终极目标，这样系统就不会那么“傻缺”了
![[Pasted image 20260314204123.png]]
- 两个函数
	- src = $(wildcard  ./* .c)  : 匹配当前工作目录下的所有.c文件。将文件名组成列表，赋值给变量src (wildcard是函数名，后面的是传入的参数)    比如：src = add.c  sub.c div1.c
	- obj = $(patsubst %.c, %.o ，%(src)) : 将参数3中，包含参数1的部分，替换成参数2，赋值给变量obj（写的时候‘，’不要省略  ）   比如src = add.c sub.c div1.c 
	; 替换了之后 obj = add.o sub.o div1.o 
![[Pasted image 20260314211310.png]]
- 三个自动变量
	$@: 在规则的命令中(就是有缩进的那一行，它上面的哪一行不能用)，表示规则中的目标
	$^:  在规则的命令中，表示所有依赖条件
	$<:  在规则的命令中，表示第一个依赖条件
![[Pasted image 20260315212235.png]]
模式规则：
	%.o : %.c
		gcc -c $< -o $@

静态模式规则：指定某个模式规则给谁用
![[Pasted image 20260315215751.png]]
![[Pasted image 20260315215758.png]]

伪目标：
	`.PHONY clean ALL`
![[Pasted image 20260315220216.png]]
- 作用：防止当前目录中有clean文件或者ALL文件影响`make clean`命令的执行


clean:
![[Pasted image 20260315210123.png]]
![[Pasted image 20260315210133.png]]

- 执行：make clean [-n] 
	- make clean ：执行：rm -rf $(obj) a.out
	- 执行make clean -n :  屏幕中显示将要执行的命令： `rm -rf $(obj) a.out` ,但不去真的执行。起一个预览功能
	- rm前面的 - 是干什么的：删除不存在的文件的时候，不会报错





- 一个很标准，符合可移植规则的makefile
- 作用：编译出server.o server
```cpp
# 定义编译参数：开启所有警告(-Wall) + 生成调试信息(-g)
# 这和你手动敲 -Wall -g 是一样的
CFLAGS = -Wall -g

# 定义目标程序名（最终生成的可执行文件叫 "server"）
TARGET = server

# 定义源文件（自动查找当前目录下所有的 .c 文件，这里只有 server.c）
SRCS = $(wildcard *.c)

# 将 .c 后缀替换成 .o 后缀（生成 server.o）
OBJS = $(patsubst %.c, %.o, $(SRCS))

# 1. 默认目标：执行 make 时直接编译出服务器
all: $(TARGET)

# 2. 生成目标程序 server
# 依赖是 server.o，gcc 会把它链接成可执行文件
$(TARGET): $(OBJS)
	$(CC) $^ -o $@ $(CFLAGS)

# 3. 生成 .o 文件
# 规则：任何 .c 文件变化，都会重新编译
%.o: %.c
	$(CC) -c $< -o $@ $(CFLAGS)

# 4. 清理命令：执行 make clean 删除生成的文件
clean:
	rm -rf $(OBJS) $(TARGET)

# 5. 声明伪目标，防止目录里有名为 clean/all 的文件冲突
.PHONY: all clean
```
