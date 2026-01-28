---
data: 2025-11-12
---
- string substr(int pos=0,int n=npos) const  //返回由pos开始的n个字符组成的字符串

```
//字串获取
//string substr(int pos=0,int n=npos) const  //返回由pos开始的n个字符组成的字符串
#include<iostream>
using namespace std;
#include<string>

void test1() {
	string str = "abcdef";
	
	string subStr = str.substr(1,3);
	
	cout << "subStr=" << subStr << endl;//输出bcd
}

//实用操作
void test2() {
	string email = "lliangzeyuan@qq.com";
	int pos = email.find('@');
	string userName = email.substr(0, pos);
	cout << "userName=" << userName << endl;//输出lliangzeyuan
}
int main() {
	//test1();
	test2();
	system("pause");
	return 0;
}     





```