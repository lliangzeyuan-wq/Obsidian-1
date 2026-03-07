---
data: 2026-03-07
---
# C++ map 中调用存储的 function 对象的方法笔记

## 前置说明

场景：map 中存储`<string, function<int(int, int)>>`类型的键值对（如`"+"`对应加法函数、`"-"`对应减法函数），核心是先获取 map 中的`function`对象，再像普通函数一样传参调用。

基础示例定义：

cpp

运行

```
#include <iostream>
#include <functional>
#include <map>
using namespace std;

int add(int x, int y) { return x + y; }
int sub(int x, int y) { return x - y; }

int main() {
    map<string, function<int(int, int)>> op_map{
        {"+", add},
        {"-", sub}
    };
    // 下文调用示例均基于此op_map
    return 0;
}
```

## 调用方式

### 方式 1：[] 运算符（最常用，简洁）

- **用法**：`map[键](参数列表)`
- **特点**：
    
    - 语法简洁，开发效率高；
    - 若键不存在，会自动插入「默认构造的空 function」（隐藏风险，可能导致后续逻辑异常）；
    - 适合**确定键一定存在**的场景。
    
- **示例代码**：

cpp

运行

```
// 调用"+"对应的加法函数
cout << op_map["+"](2, 3) << endl;  // 输出：5
// 调用"-"对应的减法函数
cout << op_map["-"](2, 3) << endl;  // 输出：-1
```

### 方式 2：at () 方法（安全，键不存在抛异常）

- **用法**：`map.at(键)(参数列表)`
- **特点**：
    
    - 键不存在时抛出`out_of_range`异常，不会插入空元素；
    - 适合**需要严格校验键是否存在**的场景（如核心业务逻辑）。
    
- **示例代码**：

cpp

运行

```
try {
    cout << op_map.at("+")(2, 3) << endl;  // 输出：5
    cout << op_map.at("*")(2, 3) << endl;  // 键不存在，抛出异常
} catch (out_of_range& e) {
    cout << "错误：" << e.what() << endl;  // 捕获异常并提示
}
```

### 方式 3：find () + 迭代器（最安全，无异常 / 无多余插入）

- **用法**：先通过`find()`获取迭代器 → 判断是否找到（迭代器≠map.end ()）→ 调用`it->second(参数)`
- **特点**：
    
    - 键不存在时返回`map.end()`，既不抛异常也不插入空元素；
    - 工业级代码首选，兼顾安全性和性能。
    
- **示例代码**：

cpp

运行

```
// 查找"+"并调用
auto it = op_map.find("+");
if (it != op_map.end()) {
    cout << it->second(2, 3) << endl;  // it->first=键，it->second=function对象
} else {
    cout << "键不存在" << endl;
}
```

### 方式 4：遍历 map（批量调用所有元素）

- **用法**：遍历 map 的键值对，逐个调用`function`对象
- **特点**：适合**批量执行 map 中所有函数**的场景（如批量测试、批量处理指令）。
- **示例代码**：

cpp

运行

```
// 范围for遍历（C++11+）
for (auto& pair : op_map) {
    cout << pair.first << ": " << pair.second(2, 3) << endl;
}
// 输出：
// +: 5
// -: -1
```

## 核心总结

表格

|调用方式|优点|缺点|适用场景|
|---|---|---|---|
|[] 运算符|语法简洁、开发效率高|键不存在时自动插入空元素|确定键存在的快速开发场景|
|at () 方法|键不存在抛异常，易排查|需捕获异常，语法稍繁琐|严格校验键存在的核心逻辑|
|find ()+ 迭代器|最安全，无异常 / 无插入|代码稍多|工业级生产代码（首选）|
|遍历 map|批量处理所有元素|仅适用于全量调用场景|批量测试、全量指令执行|

## 实战场景（聊天服务器项目）

该写法可用于「指令 - 处理函数」映射（解耦业务逻辑）：

cpp

运行

```
// 模拟聊天服务器：指令映射到处理函数
map<string, function<void(string)>> cmd_map{
    {"login", [](string msg){ cout << "处理登录：" << msg << endl; }},
    {"chat",  [](string msg){ cout << "处理聊天：" << msg << endl; }}
};

// 模拟客户端指令
string cmd = "login";
string msg = "user:tony,pwd:123";
// 调用对应处理函数
auto it = cmd_map.find(cmd);
if (it != cmd_map.end()) {
    it->second(msg);  // 输出：处理登录：user:tony,pwd:123
}
```