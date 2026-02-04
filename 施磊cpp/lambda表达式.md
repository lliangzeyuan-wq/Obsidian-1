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
	y += 1, 5;//111
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
	y += 1, 5;//222
	cout << p(10, 20) << endl;
}
```