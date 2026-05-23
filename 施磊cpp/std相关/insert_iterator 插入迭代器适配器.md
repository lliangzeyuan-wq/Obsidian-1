
[std::insert_iterator - 搜索](https://cn.bing.com/search?q=std%3A%3Ainsert_iterator+&form=ANNTH1&refig=6a11671a09b44895807d59a8504f91c0&pc=CNNDDB&adppc=EDGEDBB)
# C++ 中的 `std::insert_iterator` 笔记

## 一、基本概念

`std::insert_iterator` 是 C++ STL 提供的**插入迭代器适配器**，用于将元素插入到容器的指定位置，而不是覆盖原有元素。

它通过调用容器的 `insert()` 方法实现插入操作，因此适用于**所有支持 `insert()` 方法的容器**，包括序列容器和关联容器。

---

## 二、基本用法

`std::insert_iterator` 的定义需要指定目标容器和插入位置。

### 语法

```cpp
std::insert_iterator<Container> insert_it(container, it);
```

- `Container`：目标容器的类型
- `container`：目标容器的实例
- `it`：基础迭代器，表示插入位置

### 辅助函数

C++ 提供了辅助函数 `std::inserter()`，可以更方便地创建 `std::insert_iterator`：

```cpp
std::inserter(container, it);
```

---

## 三、示例代码

以下代码展示了如何使用 `std::insert_iterator` 向 `std::list` 容器的指定位置插入元素：

```cpp
#include <iostream>
#include <iterator>
#include <list>

int main() {
    // 1. 初始化列表：创建一个包含两个 5 的 list
    std::list<int> foo = {5, 5};
    // 2. 指定插入位置：++foo.begin() 指向第二个元素（第二个 5）的位置
    auto it = ++foo.begin();

    // 3. 创建插入迭代器：绑定到 foo，插入位置为 it
    std::insert_iterator<std::list<int>> insert_it = std::inserter(foo, it);

    // 4. 插入元素：每次赋值都会在指定位置插入元素，并自动后移迭代器
    insert_it = 1;
    insert_it = 2;
    insert_it = 3;
    insert_it = 4;

    // 5. 输出结果
    for (const auto& elem : foo) {
        std::cout << elem << " ";
    }
    return 0;
}
```

我们一步一步看变化：

1. 初始状态：`foo = [5, 5]`，`it` 指向第二个 `5`
2. `insert_it = 1`：在 `it` 位置插入 `1` → `foo = [5, 1, 5]`，`it` 自动后移，指向原来的第二个 `5`（现在在索引 2）
3. `insert_it = 2`：在 `it` 位置插入 `2` → `foo = [5, 1, 2, 5]`，`it` 再后移，指向原来的第二个 `5`（现在在索引 3）
4. `insert_it = 3`：插入 `3` → `foo = [5, 1, 2, 3, 5]`，`it` 指向原来的第二个 `5`（现在在索引 4）
5. `insert_it = 4`：插入 `4` → `foo = [5, 1, 2, 3, 4, 5]`，`it` 指向原来的第二个 `5`（现在在索引 5）

### 输出结果

plaintext

```
5 1 2 3 4 5
```

---

## 四、特点与注意事项

1. **适用容器**
    
    `std::insert_iterator` 适用于所有支持 `insert()` 方法的容器：
    
    - 序列容器：`std::vector`、`std::list` 等
    - 关联容器：`std::set`、`std::map` 等
    
2. **关联容器的插入**
    
    对于关联容器（如 `std::set`、`std::map`），插入位置仅作为提示，实际插入位置由容器内部排序规则决定。如果提示位置不准确，可能会降低插入效率。
    
3. **与 `std::copy` 配合使用**
    
    `std::insert_iterator` 常与 `std::copy` 等算法结合使用，用于将一个容器的内容插入到另一个容器的指定位置。
    
    cpp
    
    运行
    
    ```cpp
    std::list<int> source = {1, 2, 3};
    std::list<int> target = {10, 20};
    auto it = ++target.begin(); // 指定插入位置（在 10 和 20 之间）
    std::copy(source.begin(), source.end(), std::inserter(target, it));
    // target 结果：10 1 2 3 20
    ```
    
4. **效率问题**
    
    - 对于序列容器（如 `std::list`），插入效率较高
    - 对于关联容器，插入效率可能受提示位置的准确性影响，合理选择插入位置可以提高性能
    

---

## 五、总结

通过 `std::insert_iterator`，可以灵活地向容器的任意位置插入元素，极大地增强了 STL 算法的适用性和扩展性。


## 六、自动推导模板参数

有两种等价写法，推荐用 `std::inserter` 辅助函数，更简洁也不容易写错：

#### 写法 1：直接用模板参数

cpp

运行

```
std::insert_iterator<std::vector<std::string>> ins(keys, keys.begin());
```

- `std::vector<std::string>`：指明目标容器类型
- `keys`：目标容器实例
- `keys.begin()`：插入位置迭代器

#### 写法 2：用 `std::inserter`（推荐）

cpp

运行

```
auto ins = std::inserter(keys, keys.begin());
```

`std::inserter` 会自动帮你推导模板参数，不用手动写，是更安全、更现代的写法。