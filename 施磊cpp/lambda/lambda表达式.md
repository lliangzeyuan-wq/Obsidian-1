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


### 用[this],[* this]访问类中的成员
>[this] 
>/按引用捕获/

>[* this]
>/按值捕获



eg:[this]  /按引用捕获/
```cpp
#include<iostream>
using namespace std;
class DemoClass {
private:
	int m_id;
public:
	DemoClass(int id):m_id(id){}
	void print() {
		[this](){
			m_id++;
			cout << "id = " << m_id << "\n";
			}();
	}
};

int main() {
	DemoClass demo(100);
	demo.print();
}
```


eg:[* this]  /按值捕获
```cpp
#include<iostream>
using namespace std;
class DemoClass {
private:
	int m_id;
public:
	DemoClass(int id):m_id(id){}
	void print() {
		[*this]()mutable {//111
			m_id++;//222
			cout << "id = " << m_id << "\n";
			}();
	}
};

int main() {
	DemoClass demo(100);
	demo.print();
}
```
- 如果没有//111 处的mutable，那么//222 处的m_id++就是错误的



---

### 返回类型的省略

**何时不需要写`->返回类型`
- 只有一条语句

**合适要写**
#### 例子 1：多条语句，必须显式声明

这是最标准的场景。当 lambda 函数体中有**两条及以上语句**（比如加了打印、计算中间值），编译器无法自动推导，**必须**写 `-> 返回类型`。


```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<pair<int, int>> v = {{3, 1}, {1, 4}, {2, 2}};

    // 需求：排序时打印比较过程（多条语句）
    sort(v.begin(), v.end(), [](const pair<int, int>& a, const pair<int, int>& b) -> bool {
        // 语句1：打印日志（竞赛中调试常用）
        cout << "比较: " << a.second << " 和 " << b.second << endl;
        // 语句2：返回比较结果
        return a.second < b.second;
    });

    return 0;
}
```

**关键点**：如果去掉 `-> bool`，这段代码会直接编译报错。

#### 例子 2：分支结构，返回类型不一致（隐式陷阱）

即使只有一条 `return`，但在**条件判断**中，如果两个分支返回的类型看起来不一样（比如一个是 `int`，一个是 `bool`），也需要显式声明。


```cpp
// 需求：根据flag决定升序或降序，且返回值被隐式转换
auto compare = [](int a, int b, bool asc) -> bool {
    if (asc) {
        return a < b; // 返回 bool
    } else {
        return 0;     // 返回 int，虽然能转成 bool，但编译器会迷茫
    }
};
```

**关键点**：为了代码的健壮性，在蓝桥杯写复杂比较器时，建议养成**直接写 `-> bool`** 的好习惯。


### 例子 3：捕获外部变量 + 复杂逻辑（竞赛实战）

在蓝桥杯的**贪心算法**或**动态规划**中，经常需要 lambda 捕获外部的数组或变量来进行复杂比较。



```cpp
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> nums = {5, 3, 8, 1};
    vector<int> weight = {2, 5, 1, 3}; // 外部权重数组

    // 需求：按照 (数值 * 权重) 的结果降序排序
    sort(nums.begin(), nums.end(), [&](int a, int b) -> bool {
        // 先通过数值找到对应的权重（这是一个复杂的查找过程）
        auto find_w = [&](int x) {
            for (int i = 0; i < nums.size(); i++) {
                if (nums[i] == x) return weight[i];
            }
            return 0;
        };
        // 计算最终得分并比较
        int scoreA = a * find_w(a);
        int scoreB = b * find_w(b);
        return scoreA > scoreB; // 降序
    });

    return 0;
}
```

**关键点**：这里 lambda 体内定义了另一个 lambda，还有循环和变量计算，必须显式声明 `-> bool` 才能通过编译。