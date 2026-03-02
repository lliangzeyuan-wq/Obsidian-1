---
data: 2026-03-02
---
这是 C++ STL 里的一个非常常用的算法函数：**`std::upper_bound`**。

---

### 1. 它是干嘛的？

`upper_bound` 用于在**已排序的区间**中，找到**第一个大于目标值的元素**的位置。

- 前提：区间必须是**按升序排好序**的。
- 作用：返回一个迭代器，指向第一个 `> value` 的元素。
- 如果所有元素都 `≤ value`，则返回区间末尾的迭代器（`end()`）。

---

### 比较pair<>时的规则
- 先比较第一个pair . first  ，如果.first相等，再比较第二个。
- 下面的代码`make_pair(t, INT_MAX)` 保证了当.first 相等的时候，其他的所有的都比这个要小，只能往下走去确保.first比这个要大
```cpp
auto it = upper_bound(soldiers.begin(), soldiers.end(), make_pair(t, INT_MAX));
int pos = it - soldiers.begin();
```


---

### 3. 和 `lower_bound` 的区别（面试常考）

|函数|作用|返回值|
|:--|:--|:--|
|`lower_bound`|找第一个 **≥** 目标值的元素|第一个不小于目标值的位置|
|`upper_bound`|找第一个 **>** 目标值的元素|第一个大于目标值的位置|

在这道题里，我们用 `upper_bound` 是为了精确地把 `c_i ≤ t` 和 `c_i > t` 的士兵分开，这样才能正确计算需要额外训练的人数和花费。