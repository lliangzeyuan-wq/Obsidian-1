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

### 封装类的成员函数/变量
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

- 在这个例子里，//111 处封装的类里面的`float f(float x) { return k_ * x + b_; };`    在参数列表那一部分，他第一个参数是==`Linear&`==    。//333 处，封装类里面的成员变量k_时，本来应该是`function<float>k`  ，但是是类里面的，因此要变成`function<float(Linear&)>k`   



### 类型擦除模式
- 类型擦除模式：通过`std::function`可以把完全不同的类型，按照同一个接口，也就是函数签名，统一封装成一个类型来使用
```cpp
#include<iostream>
#include<functional>
#include<map>
using namespace std;
float add(float a, float b) {
	return a + b;
}
struct Substract {
	float operator()(float a, float b) {
		return a - b;
	}
};

int main() {
	map<char, function<double(double, double)>>calculator{
		{'+',add},
		{'-',Substract()},
		{'*',[](double a,double b)->double {return a * b; }}
	};

	cout << calculator['+'](12.0,13) << endl;
	cout << calculator['-'](13.0, 6.2) << endl;
	cout<< calculator['*'](2.3, 3.2) << endl;
}
```