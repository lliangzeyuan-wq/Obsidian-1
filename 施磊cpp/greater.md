

先给你一句话结论：

**`std::greater<T>` 是一个 “仿函数”，作用就是：拿两个数 a、b，返回 `a > b` 的结果。**

头文件：**`#include <functional>`**

下面拆开讲，尽量直白。

---

## 1. greater 本质是什么？

它是一个**模板结构体（仿函数）**，可以当成 “能像函数一样用的比较规则”。

简化版实现（标准库大概长这样）：

cpp

运行

```
template <class T>
struct greater {
    bool operator()(const T& a, const T& b) const {
        return a > b;
    }
};
```

你调用 `greater<int>()(x, y)`，等价于问：**x > y 吗？**

---

## 2. 和 less 对比（最容易混）

- `less<T>` → 返回 `a < b`
- `greater<T>` → 返回 `a > b`

默认很多容器 / 算法用的是 **less**：

- `sort` 默认：从小到大（升序）
- `priority_queue` 默认：大根堆

---

## 3. 在 priority_queue 里为什么它能变成小根堆？

重点来了！`priority_queue` 比较逻辑和 `sort` **反过来**：

### 3.1 sort 的逻辑

`sort` 认为：**comp (a,b) 为 true 时，a 排到 b 前面**

- `sort(..., less<int>())` → a < b → 小的在前 → 升序
- `sort(..., greater<int>())` → a > b → 大的在前 → 降序

### 3.2 priority_queue 的逻辑（关键）

堆的规则：**comp (a,b) 为 true 时，a 的优先级比 b 低（b 更靠近堆顶）**

默认：

cpp

运行

```
priority_queue<int> pq;
// 等价于
priority_queue<int, vector<int>, less<int>> pq;
```

- `less<int>`：a < b → true → **a 优先级低，b 在上** → 大根堆

改成：

cpp

运行

```
priority_queue<int, vector<int>, greater<int>> pq;
```

- `greater<int>`：a > b → true → **a 优先级低，b 在上** → 小根堆

所以：

- **less → 大根堆**
- **greater → 小根堆**

---

## 4. 套到你 Dijkstra 的 pair 小根堆

cpp

运行

```
using P = pair<uint, int>;
priority_queue<P, vector<P>, greater<P>> pq;
```

`pair` 默认先比 **first（距离）**，再比 second（顶点号）。

`greater<P>` 意味着：

- 两个 pair a、b
- `a > b` → true → a 优先级低 → **距离小的在堆顶**
    
    正好符合 Dijkstra：**每次取当前最短距离的点**。

---

## 5. 最简记忆

- **greater = 比大小用的仿函数：a > b**
- **sort：less 升序、greater 降序**
- **priority_queue：less 大根、greater 小根**
- **Dijkstra 小根堆：必须用 greater**
