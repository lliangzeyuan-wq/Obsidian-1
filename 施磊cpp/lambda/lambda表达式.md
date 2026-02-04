---
data: 2026-02-04
---
[一起来学C++ 32.Lambda表达式_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13MmUYUE9S/?spm_id_from=333.337.search-card.all.click&vd_source=43c9de78f6e5f2b05790188e274ad943)
### 格式
- 可选限定符 不常用，一般省略
![[Pasted image 20260204145859.png]]



### 一个例子
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [x, y](int a, int b)->float {
		return x * y + a * b;
		};
	cout << p(10, 20);
}
```

- 上面的这个lambda表达式换成函数对象的写法之后实际上就是下面这个样子
```cpp
int main() {
	int x = 7;
	float y = 3.0;

	struct {
		int _x;
		float _y;
		float operator()(int a, int b)const {
			return _x * _y + a * b;
		}
	}p{ x,y };
}
```

### 捕获方式

###### 按值捕获
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [x, y](int a, int b)->float {
		return x * y + a * b;
		};
	cout << p(10, 20) << endl;
	y += 1.5;//111
	cout << p(10, 20) << endl;
}
```
> 输出
> 221
> 221

- //111  处虽然修改了y的值，但是由于lambda函数对象中的对应成员变量只是在初始化时复制了y的值，所以结果不会发生变化

###### 引用捕获
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [&x,&y](int a, int b)->float {
		return x * y + a * b;
		};
	cout << p(10, 20) << endl;
	y += 1.5;//222
	cout << p(10, 20) << endl;
}
```
>输出
>221
>231.5

- 因为使用了引用，因此lambda函数对象外部成员发生变化的时候，结果会发生变化

### 按值捕获不可修改，引用捕获可以修改
###### lambda所对应的函数调用运算符，默认是 const函数，因此函数内部，不能修改按值捕获的成员，但可以修改引用捕获的成员
eg:
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [x, &y](int a, int b)->float {
		x++;//111
		y++;//222
		return x * y + a * b;
		};
	cout << p(10, 20) << endl;
	y += 1.5;
	cout << p(10, 20) << endl;
}
```
- //111 处是错误的，//222 处是正确的

###### 但是如果给lambda函数加上mutable限制符，那么对应的调用函数就不再是const函数，捕获的成员都是可以修改的


### 默认捕获方式

>//**默认按值捕获**
>[=]

>//**默认引用捕获**
>[&]


eg:默认按值捕获
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [=](int a, int b)->float {
		return x * y + a * b;
		};
}
```


eg:默认引用捕获
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [&](int a, int b)->float {
		return x * y + a * b;
		};
}
```

- 默认捕获和显式捕获同时使用（默认捕获和显式捕获不能是同类型的）
eg:
```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [=,&y](int a, int b)->float {
		return x * y + a * b;
		};
	cout << p(10, 20);
}
```

```cpp
int main() {
	int x = 7;
	float y = 3.0;
	auto p = [&,x](int a, int b)->float {
		return x * y + a * b;
		};
	cout << p(10, 20);
}
```



### 引用捕获容易出现的错误
- 错误代码
```cpp
auto createLambda() {
	float x = 1.0;
	float y = 2.0;
	return [&](float a) {return a * x + y; };
}

int main(void) {
	auto f = createLambda();
	cout << f(2.0) << endl;
}
```
- x和y是createLambda函数里的局部变量，当函数返回时，这些变量的声明周期结束了，内存被收回。你的Lambda表达式又是引用捕获，因此是错误的

- 正确的代码：改引用捕获为按值捕获
```cpp
auto createLambda() {
	float x = 1.0;
	float y = 2.0;
	return [=](float a) {return a * x + y; };
}

int main(void) {
	auto f = createLambda();
	cout << f(2.0) << endl;
}
```


### 用