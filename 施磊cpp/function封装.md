---
data: 2026-02-05
---



### 封装函数
```cpp
#include<iostream>
#include<functional>
using namespace std;
double multiply(double a, double b) {
	return a * b;
}
int main() {
	function<double(double, double)>func1 = multiply;
	double res = func1(1.1, 2.3);
	cout << res << endl;
	return 0;
}
```
- `function<double(double,double)>`     第一个double是函数的返回值，第二、三个double是函数参数列表中的东西

### 封装类的成员函数
```cpp

```