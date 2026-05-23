[std::this_thread - 搜索](https://cn.bing.com/search?q=std%3A%3Athis_thread&form=ANNTH1&refig=6a1180cfa5014a99a862bdf7da4bef75&pc=CNNDDB&adppc=EDGEDBB)


# std::this_thread 在 C++ 中的使用笔记

`std::this_thread` 是 C++ 标准库中的一个命名空间，提供了一组用于访问和操作当前线程的工具函数，核心功能包括获取线程 ID、线程休眠、主动让出 CPU 时间片等。

---

## 1. 获取当前线程 ID

使用 `std::this_thread::get_id()` 可以获取当前线程的唯一标识符，返回值类型为 `std::thread::id`，可用于线程比较、日志记录等场景。

### 示例代码

cpp

运行

```
#include <iostream>
#include <thread>

int main() {
    // 获取并输出当前线程的 ID
    std::thread::id this_id = std::this_thread::get_id();
    std::cout << "当前线程 ID: " << this_id << std::endl;
    return 0;
}
```

---

## 2. 使当前线程休眠

`std::this_thread` 提供了两个线程休眠函数，区别在于时间参数的类型：

表格

|函数|功能|参数类型|
|---|---|---|
|`std::this_thread::sleep_for`|休眠指定的**时间段**|相对时间（如 `std::chrono::seconds`）|
|`std::this_thread::sleep_until`|休眠直到指定的**时间点**|绝对时间（如 `std::chrono::system_clock::time_point`）|

### `sleep_for` 示例（倒计时）

cpp

运行

```
#include <iostream>
#include <thread>
#include <chrono>

int main() {
    std::cout << "倒计时:\n";
    for (int i = 10; i > 0; --i) {
        std::cout << i << std::endl;
        // 线程休眠 1 秒
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
    std::cout << "倒计时结束！" << std::endl;
    return 0;
}
```

---

## 3. 补充功能（扩展）

### 主动让出 CPU 时间片

`std::this_thread::yield()` 函数会主动将当前线程的 CPU 时间片让渡给其他就绪线程，适用于自旋锁、忙等待场景下减少 CPU 占用。

cpp

运行

```
#include <iostream>
#include <thread>

int main() {
    // 主动让出 CPU 时间片
    std::this_thread::yield();
    std::cout << "当前线程已主动让出 CPU 时间片" << std::endl;
    return 0;
}
```

---

## 4. 核心使用场景总结

表格

|功能|场景示例|
|---|---|
|`get_id()`|多线程日志标记、线程身份校验|
|`sleep_for()`/`sleep_until()`|定时任务、倒计时、线程间延迟执行|
|`yield()`|忙等待优化、减少无意义 CPU 占用|

---

💡 注意：这些函数都定义在 `<thread>` 头文件中，使用时需包含该头文件，且编译时需链接线程库（如 GCC 需加 `-pthread` 参数）。

