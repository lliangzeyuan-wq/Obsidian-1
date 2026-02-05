---
data: 2026-02-05
---



### 一个例子
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