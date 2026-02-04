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