---
data: 2025-11-14
---
```
- `string => int`：表示**字符串转整数**。
    
    - `stoi`：是 “string to int” 的缩写，用于将`std::string`类型转换为`int`类型。
    - `stol`：是 “string to long” 的缩写，用于将`std::string`类型转换为`long`类型（适用于数值范围更大的场景）。
- `int => string`：表示**整数转字符串**。
    
    - `to_string()`：是 C++11 引入的函数，能将`int`、`long`、`double`等数值类型转换为对应的`std::string`类型。
```
- 使用stoi   stol   to_string 都要包含#include< string >头文件