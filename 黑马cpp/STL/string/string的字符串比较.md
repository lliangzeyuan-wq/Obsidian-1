---
data: 2025-11-11
---
- 就一个compare函数
- 从第一个字母开始逐个的比较（按ASCII码的大小确定）
- 由第一个不相等的确定比较结果的大小
```
//字符串的比较
#include<iostream>
using namespace std;
#include<string>
void test1() {
	string str1 = "baaaaaa";
	string str2 = "axxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
	if (str1.compare(str2)==0) {
		 cout<<"str1=str2" << endl;
	}
	else if(str1.compare(str2)>0){
		cout << "str1>str2" << endl;
	}
	else {
		cout << "<str1<str2" << endl;
	}
}
int main() {
	test1();
	system("pause");
	return 0;
}     





```