---
data: 2026-04-21
---
[adsfjs](https://www.runoob.com/cplusplus/cpp-libs-array.html)

# C++ 容器类 `<array>`

C++11 标准引入了 `<array>` 头文件，它提供了一种固定大小的数组容器，与 C 语言中的数组相比，具有更好的类型安全和内存管理特性。

`std::array` 是 C++ 标准库中的一个模板类，它定义在 `<array>` 头文件中。`std::array` 模板类提供了一个固定大小的数组，其大小在编译时确定，并且不允许动态改变。

### 语法

`std::array` 的基本语法如下：

#include <array>

std::array<T, N> array_name;

- `T` 是数组中元素的类型。
- `N` 是数组的大小，必须是一个非负整数。

### 声明与初始化

`<array>` 需要在编译时确定大小，不能动态改变。使用示例：

#include <iostream>
#include <array>

int main() {
    std::array<int, 5> arr = {1, 2, 3, 4, 5}; // 声明一个定长为5的int数组
    return 0;
}

## 特点

- **类型安全**：`std::array` 强制类型检查，避免了 C 语言数组的类型不安全问题。
- **固定大小**：数组的大小在编译时确定，不能在运行时改变。
- **内存连续**：`std::array` 的元素在内存中是连续存储的，这使得它可以高效地访问元素。
- **标准容器**：`std::array` 提供了与 `std::vector` 类似的接口，如 `size()`, `at()`, `front()`, `back()` 等。

## 实例

下面是一个使用 `std::array` 的简单示例，包括输出结果。

## 实例

#include <iostream>  
#include <array>  
  
int main() {  
    // 创建一个包含 5 个整数的 std::array  
    std::array<int, 5> myArray = {1, 2, 3, 4, 5};  
  
    // 使用范围 for 循环遍历数组  
    for (const auto& value : myArray) {  
        std::cout << value << " ";  
    }  
    std::cout << std::endl;  
  
    // 使用索引访问数组元素  
    std::cout << "Element at index 2: " << myArray.at(2) << std::endl;  
  
    // 获取数组的大小  
    std::cout << "Array size: " << myArray.size() << std::endl;  
  
    // 修改数组元素  
    myArray[3] = 10;  
  
    // 再次遍历数组以显示修改后的元素  
    for (const auto& value : myArray) {  
        std::cout << value << " ";  
    }  
    std::cout << std::endl;  
  
    return 0;  
}  

输出结果:

1 2 3 4 5 
Element at index 2: 3
Array size: 5
1 2 3 10 5 

---

## 常用成员函数

以下是 `<array>` 中的一些常用成员函数：

|函数|说明|
|---|---|
|`[at(size_t pos)](https://www.runoob.com/cplusplus/cpp-libs-array-at.html)`|返回指定位置的元素，带边界检查|
|`[operator[]](https://www.runoob.com/cplusplus/cpp-libs-array-operator.html)`|返回指定位置的元素，不带边界检查|
|`[front()](https://www.runoob.com/cplusplus/cpp-libs-array-front.html)`|返回数组的第一个元素|
|`[back()](https://www.runoob.com/cplusplus/cpp-libs-array-back.html)`|返回数组的最后一个元素|
|`[data()](https://www.runoob.com/cplusplus/cpp-libs-array-data.html)`|返回指向数组数据的指针|
|`[size()](https://www.runoob.com/cplusplus/cpp-libs-array-size.html)`|返回数组大小（固定不变）|
|`[fill(const T& value)](https://www.runoob.com/cplusplus/cpp-libs-array-fill.html)`|将数组所有元素设置为指定值|
|`[swap(array& other)](https://www.runoob.com/cplusplus/cpp-libs-array-swap.html)`|交换两个数组的内容|
|[`begin()` / `end()`](https://www.runoob.com/cplusplus/cpp-libs-array-swap-begin-end.html)|返回数组的起始/结束迭代器|

### 实例

**1、基本操作**

## 实例

#include <iostream>  
#include <array>  
  
int main() {  
    std::array<int, 5> arr = {10, 20, 30, 40, 50};  
  
    std::cout << "Array elements: ";  
    for (int i = 0; i < arr.size(); ++i) {  
        std::cout << arr[i] << " ";  
    }  
    std::cout << std::endl;  
  
    // 获取第一个和最后一个元素  
    std::cout << "First element: " << arr.front() << std::endl;  
    std::cout << "Last element: " << arr.back() << std::endl;  
  
    return 0;  
}  

**使用 at 和边界检查**

## 实例

#include <iostream>  
#include <array>  
  
int main() {  
    std::array<int, 3> arr = {1, 2, 3};  
  
    try {  
        std::cout << arr.at(2) << std::endl;  // 正常输出  
        std::cout << arr.at(5) << std::endl;  // 超出范围，抛出异常  
    } catch (const std::out_of_range& e) {  
        std::cout << "Exception: " << e.what() << std::endl;  
    }  
  
    return 0;  
}  

**3、使用 fill 填充元素**

## 实例

#include <iostream>  
#include <array>  
  
int main() {  
    std::array<int, 5> arr;  
    arr.fill(100);  // 将所有元素设置为100  
  
    std::cout << "Filled array: ";  
    for (const auto& elem : arr) {  
        std::cout << elem << " ";  
    }  
    std::cout << std::endl;  
  
    return 0;  
}  

**4、数组交换**

## 实例

#include <iostream>  
#include <array>  
  
int main() {  
    std::array<int, 3> arr1 = {1, 2, 3};  
    std::array<int, 3> arr2 = {4, 5, 6};  
  
    arr1.swap(arr2);  
  
    std::cout << "Array 1: ";  
    for (const auto& elem : arr1) {  
        std::cout << elem << " ";  
    }  
    std::cout << std::endl;  
  
    std::cout << "Array 2: ";  
    for (const auto& elem : arr2) {  
        std::cout << elem << " ";  
    }  
    std::cout << std::endl;  
  
    return 0;  
}  

---

## 与其他数组类型对比

|特性|`std::array`|C 风格数组|`std::vector`|
|---|---|---|---|
|**大小**|编译时固定|编译时固定|动态可变|
|**边界检查**|`at()` 提供边界检查|无|`at()` 提供边界检查|
|**内存管理**|栈上分配|栈上分配|堆上分配|
|**性能**|高效|高效|较低（动态分配）|
|**接口**|支持 STL 标准接口|不支持 STL 标准接口|支持 STL 标准接口|

`std::array` 是 C++ 标准库中一个非常有用的容器，它提供了固定大小的数组，具有类型安全和内存管理的优势。通过上述示例，我们可以看到如何创建、访问和修改 `std::array` 的元素。对于需要固定大小数组的场景，`std::array` 是一个非常好的选择。