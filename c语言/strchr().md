---
data: 2026-03-18
---
Csguide.cn/roadmap/cpp/how_to_learn_cpp.html#_3-2-系统编程基础
# C 库函数 - strchr()

 [![C 标准库 - <string.h>](https://www.runoob.com/images/up.gif) C 标准库 - <string.h>](https://www.runoob.com/cprogramming/c-standard-library-string-h.html)

## 描述

strchr() 用于查找字符串中的一个字符，并返回该字符在字符串中第一次出现的位置。

strchr() 其原型定义在头文件 <string.h> 中, **char *strchr(const char *str, int c)** 在参数 **str** 所指向的字符串中搜索第一次出现字符 **c**（一个无符号字符）的位置。

strchr() 函数返回的指针指向字符串中的字符，如果要将该指针用作字符串，应该将其传递给其他字符串处理函数，例如 printf() 或 strncpy()。

## 声明

下面是 strchr() 函数的声明。

char *strchr(const char *str, int c)

## 参数

- **str** -- 要查找的字符串。
- **c** -- 要查找的字符。

## 返回值

如果在字符串 str 中找到字符 c，则函数返回指向该字符的指针，如果未找到该字符则返回 NULL。

## 实例

下面的实例演示了 strchr() 函数的用法。

## 实例

#include <stdio.h>  
#include <string.h>  
  
int main ()  
{  
   const char str[] = "https://www.runoob.com";  
   const char ch = 'o';  
   char *ptr;  
  
   ptr = strchr(str, ch);  
  
    if (ptr != NULL) {  
        printf("字符 'o' 出现的位置为 %ld。\n", ptr - str + 1);  
        printf("|%c| 之后的字符串是 - |%s|\n", ch, ptr);  
    } else {  
        printf("没有找到字符 'o' 。\n");  
    }  
   return(0);  
}  

以上实例中 strchr() 函数在字符串 "https://www.runoob.com" 中查找字符 'o' 的第一次出现。由于该字符出现在位置 15，因此指针 ptr 指向字符串中的第 16 个字符。

让我们编译并运行上面的程序，这将产生以下结果：

字符 'o' 出现的位置为 16。
|o| 之后的字符串是 - |oob.com|