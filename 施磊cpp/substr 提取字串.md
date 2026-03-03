---
data: 2026-03-03
---
在 C++ 中，`substr` 是 **`std::string` 类的成员函数**，用于从一个字符串中提取子字符串。

### 函数原型

cpp

运行

```
basic_string substr(size_t pos = 0, size_t count = npos) const;
```

### 参数说明

- `pos`：起始位置的索引（从 0 开始计数），表示从哪个字符开始提取子串。
- `count`：要提取的字符数量。如果省略该参数，或者指定的数量超过了字符串剩余长度，函数会一直提取到字符串末尾。

### 常见用法

cpp

运行

```
std::string s = "Hello, World!";
std::string sub1 = s.substr(7);      // 从索引7开始到末尾，结果为 "World!"
std::string sub2 = s.substr(0, 5);   // 从索引0开始提取5个字符，结果为 "Hello"
```

### 结合你代码中的例子

cpp

运行

```
prefix.substr(0, prefix.size()-1)
```

- `prefix` 是一个字符串。
- `prefix.size()-1` 表示提取的长度是原字符串长度减 1。
- 这行代码的作用是：从 `prefix` 的第 0 个字符开始，提取除了最后一个字符之外的所有字符，相当于**去掉字符串的最后一个字符**。