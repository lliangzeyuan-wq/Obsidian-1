---
data: 2026-03-05
---
别担心！**`istringstream` 是一个超级好用的 C++ 工具**，专门用来**处理字符串里的内容**（比如分割、提取数据），我用最通俗的方式给你讲清楚，保证一听就懂👇

# 一、`istringstream` 是什么？

1. **全称**：`std::istringstream`
2. **头文件**：必须加 `#include <sstream>`
3. **核心作用**：
    
    把**一段字符串**，变成**和 `cin` 用法一模一样的输入流**
    
    - `cin` 是从**键盘**读数据
    - `istringstream` 是从**字符串**里读数据
    

---

# 二、为什么要用它？（对应你的代码）

你的代码里有一行字符串，比如：

`"1,2,3,4"`

我们需要**按逗号分割**，把里面的数字一个个取出来。

原来用的是 C 语言的 `strtok`，它很老、会破坏原字符串、不安全；

`istringstream` 是 C++ 专门用来干这件事的，简单又安全。

---

# 三、超简单入门示例（先看懂基础）

cpp

运行

```
#include <iostream>
#include <string>
#include <sstream>  // 必须加这个头文件
using namespace std;

int main() {
    // 1. 有一个字符串
    string str = "10 20 30 40";

    // 2. 把字符串装进 istringstream，变成"输入流"
    istringstream iss(str);

    // 3. 像 cin 一样，直接从字符串里读数字！
    int num;
    while (iss >> num) {  // 和 cin >> num 用法完全一样
        cout << num << " ";
    }
    // 输出：10 20 30 40
    return 0;
}
```

✅ 核心：**`istringstream` 让字符串可以像键盘输入一样读取数据**

---

# 四、在你的代码里，它是怎么工作的？

你的代码中，读取到一行邻接表：`line = "1,2,3,5"`

我们要**按逗号分割**，取出每个数字：

cpp

运行

```
// 1. 把这行字符串转成输入流
istringstream iss(line);
string numStr;

// 2. 按逗号 分割读取每一段内容
while (getline(iss, numStr, ',')) {
    // numStr 依次拿到："1" → "2" → "3" → "5"
    // 转成整数，存到邻接表
    vertics.back().adjList_.emplace_back(stoi(numStr));
}
```

和 `strtok` 效果完全一样，但**更安全、更简单、更符合 C++ 规范**。

---

# 五、给你简化版（去掉多余的空白处理，新手更好懂）

我把你代码里的 `istringstream` 部分改成**最简写法**，完全满足需求：

cpp

运行

```
// 读取邻接表行
getline(ifs, line);

// 核心：用 istringstream 分割逗号
istringstream iss(line);
string num;
// 按逗号拆分，循环读取每个数字
while (getline(iss, num, ','))
{
    vertics.back().adjList_.emplace_back(stoi(num));
}
```

---

# 六、必记 3 个关键点

1. **必须加头文件**：`#include <sstream>`
2. **本质**：把字符串变成「可读取的输入流」
3. **用法**：和 `cin` 几乎一致，专门用来**拆分字符串、提取数据**

---

# 总结

你完全可以把 `istringstream` 理解为：

**专门用来拆字符串的工具**，替代了老旧的 `strtok`，是 C++ 处理字符串分割的标准用法。