### 完整可直接复制的代码

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> nums = {1, 2, 3};
    // 在第二个位置插入单个元素
    nums.insert(nums.begin() + 1, 100); // {1, 100, 2, 3}
    // 在末尾插入两个相同元素
    nums.insert(nums.end(), 2, 5); // {1, 100, 2, 3, 5, 5}
    // 插入另一个容器的范围
    vector<int> extra = {7, 8};
    nums.insert(nums.begin() + 2, extra.begin(), extra.end()); // {1, 100, 7, 8, 2, 3, 5, 5}
    // 使用初始化列表插入
    nums.insert(nums.end(), {9, 10}); // {1,100,7,8,2,3,5,5,9,10}

    for (int n : nums) cout << n << " ";
}
```

### vector::insert 用法文字整理（可复制）

`vector::insert` 用于在指定位置插入一个或多个元素，支持单元素、多元素、范围以及初始化列表插入

#### 主要重载形式

1. 插入单个元素

```
iterator insert(pos, const T& value);
```

在`pos`迭代器指向的位置之前插入`value`的副本，返回指向新插入元素的迭代器

2. 插入多个相同元素

```
iterator insert(pos, size_type n, const T& value);
```

在`pos`之前插入`n`份值为`value`的元素

3. 插入其他容器的区间范围

```
iterator insert(pos, InputIt first, InputIt last);
```

把区间`[first, last)`内全部元素插入到`pos`前面

4. C++11 初始化列表插入

```
iterator insert(pos, std::initializer_list<T> ilist);
```

一次性把初始化列表中的多个值插入进去

#### 注意事项

1. 插入点后方原有元素会向后挪动位置，容易造成旧的迭代器失效；
2. 如果频繁在容器头部、中部做插入操作，vector 性能偏弱，更推荐`deque`或者`list`；
3. 如果只打算往尾部追加元素，优先使用`push_back`，开销更低、效率更好。
