---
data: 2025-12-26
---
[C++ 容器类 <unordered_set> | 菜鸟教程](https://www.runoob.com/cplusplus/cpp-libs-unordered_set.html)


- 构造
`std::unordered_set<int>uset;`
- 插入
`uset.insert(10)`;
- 查找
```cpp
auto it=uset.find(10);
if(it!=uset.end())
{
	//元素存在
}
```
- 删除元素
`uset.erase(10);`
- 大小和空检查
```cpp
size_t size = uset.size();
bool isEmpty = uset.empty();
```
- qing'ko