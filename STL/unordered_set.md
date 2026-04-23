---
data: 2025-12-26
---
[C++ 容器类 <unordered_set> | 菜鸟教程](https://www.runoob.com/cplusplus/cpp-libs-unordered_set.html)

`unordered_set` 不保证元素的排序，但通常提供更快的查找、插入和删除操作。不允许元素重复



- 构造
`std::unordered_set<int>uset;`
- 插入
`uset.insert(10)`;
- 查找
-.find()
```cpp
auto it=uset.find(10);
if(it!=uset.end())
{
	//元素存在
}
```
-.count();
>如果存在，返回1 ； 如果不存在，返回0
- 删除元素
`uset.erase(10);`
- 大小和空检查
```cpp
size_t size = uset.size();
bool isEmpty = uset.empty();
```
- 清空容器
`uset.clear();`
