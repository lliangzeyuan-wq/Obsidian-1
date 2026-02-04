---
data: 2026-02-04
---
- 在lambda表达式中，有时候会使用这个IIFE
eg:
```cpp
[this]() { m_id++; cout << "id = " << m_id << "\n"; }()
```

- 最后的`()` 就是调用运算符，作用是**让刚定义好的 Lambda 立即执行一次**。


- 它的效果和你定义一个普通函数再马上调用它是一样的
等价写法：
```cpp
// 等价写法（便于理解）
 auto temp_func = [this]() { m_id++; cout << "id = " << m_id << "\n"; };
 temp_func(); // 调用这个匿名函数
```

**拓展：带参数的IIFE**
```cpp
// 带参数的IIFE 
[](int a, int b) { cout << "a+b=" << a+b << endl; }(10, 20); 
// 调用时传参 ↗                      ↖ 定义时声明参数 
// 输出：a+b=30
```
