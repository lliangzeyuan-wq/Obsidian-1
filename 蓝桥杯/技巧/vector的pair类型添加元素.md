---
data: 2026-03-01
---
向 `vector<pair<double, double>>` 中添加元素，主要有以下几种写法，核心区别在于**是直接构造元素**还是**先构造再拷贝 / 移动**：

---

### 1. 最推荐：`emplace_back`（直接在容器内构造）

这是 C++11 引入的高效写法，直接在 `vector` 的内存空间里构造 `pair`，避免了临时对象的拷贝 / 移动，性能最优：

cpp

运行

```
double l = 0.1, r_angle = 0.5;
vector<pair<double, double>> intervals;

// 直接传递两个 double，emplace_back 会调用 pair<double, double>(l, r_angle) 构造
intervals.emplace_back(l, r_angle); 
```

---

### 2. `push_back` + `make_pair`（最常用的传统写法）

用 `std::make_pair` 生成一个临时 `pair`，再通过 `push_back` 加入容器，代码可读性好，兼容性强：

cpp

运行

```
#include <utility> // 包含 make_pair

intervals.push_back(std::make_pair(l, r_angle));
```

`make_pair` 会自动根据参数类型推导 `pair` 的模板参数，这里会生成 `pair<double, double>`。

---

### 3. `push_back` + 显式 `pair` 构造

直接显式构造一个 `pair` 对象，再 `push_back`，适合需要明确指定类型的场景：

cpp

运行

```
intervals.push_back(std::pair<double, double>(l, r_angle));
```

---

### 4. `push_back` + 列表初始化（C++11 及以后）

利用 C++11 的列表初始化特性，用花括号 `{}` 直接构造 `pair`，代码更简洁：

cpp

运行

```
intervals.push_back({l, r_angle});
```

编译器会自动将 `{l, r_angle}` 推导为 `pair<double, double>`。

---

### 总结对比

|写法|特点|适用场景|
|---|---|---|
|`emplace_back(a, b)`|直接构造，无拷贝，性能最优|优先推荐，尤其是性能敏感的场景|
|`push_back(make_pair(a, b))`|可读性好，兼容性强|代码风格统一，兼容旧标准|
|`push_back(pair<double, double>(a, b))`|显式构造，类型明确|需要严格指定 `pair` 类型时|
|`push_back({a, b})`|代码简洁，现代 C++ 风格|C++11 及以上，追求代码简洁时|

在你的这道题里，`emplace_back(l, r_angle)` 是最推荐的写法，既高效又清晰。