---
data: 2026-01-19
---
```
#include <string>
#include<iostream>
using namespace std;
int main()
{
	char s[123] = "fjj";
	string s1 = "fdh";
	cout << strlen(s) << endl;
	int len = sizeof(s) / sizeof(s[0]);
	cout << len << endl;
	cout << s1.size() << endl;
}
```


输出结果为：3   123    3
- 解释
	- ==strlen(const char * )     用于计算const char * 实际占有的字符数==
	- sizeof(arr)/sizeof(s[0])     用于计算占有的内存，很鸡肋，没什么用
	- ==< string >.size()     用于计算string实际占有的字符数==
