---
data: 2026-02-16
---
 - 使用的时候必须加上`#include <memory>
 - ```cpp
   #include <memory>
   using namespace std;
   
   shared_ptr<int> p;
   p=make_shared<int>(100);
   ```
- 在上面的程序中，可以看到shared_ptr是一个模板，你要指定参数。并用make_shared来初始化
- 另一种写法
```cpp
  #include <memory>
   using namespace std;
   
   shared_ptr<int> p{make_shared<int>(100)};
```


### 一个例子
```cpp
#include <memory>
#include<iostream>
using namespace std;

class Ball {
public:
	Ball() {
		cout << "A ball appears." << endl;
	}
	~Ball() {
		cout << "A ball disappears." << endl;
	}
	void Bounce() {
		cout << "A ball jumps." << endl;
	}
};

int main() {
	shared_ptr<Ball>p = make_shared<Ball>();
	cout << p.use_count() << endl;
	shared_ptr<Ball>p2 = p;
	cout << p.use_count() << " " << p2.use_count() << endl;
	shared_ptr<Ball>p3 = p2;
	cout << p.use_count() << " " << p2.use_count()
		<< " " << p3.use_count() << endl;
	p.reset();
	p2.reset();
	p3.reset();
	return 0;
}
```

- 注意：这里`shared_ptr<Ball>p2 = p;`  不能写成  `shared_ptr<Ball>p2 = make_shared<Ball>();`   因为这样他们各自指向不同的Ball对象，彼此相互独立