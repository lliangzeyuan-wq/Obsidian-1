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
#include<iostream>
#include<functional>
using namespace std;
struct Linear {
	Linear(float k,float b):k_(k),b_(b){}
	float f(float x) { return k_ * x + b_; };
	float k_, b_;
};
int main() {
	function<float(Linear&,float)>mf = &Linear::f;//111
	Linear L(1.2, 2.3);//222
	float res = mf(L, 5);
	cout << res << endl;
	function<float(Linear&)> k = &Linear::k_;//333
	cout << k(L) << endl;
	return 0;
}
```

- 在这个例子里，//111 处封装的是里