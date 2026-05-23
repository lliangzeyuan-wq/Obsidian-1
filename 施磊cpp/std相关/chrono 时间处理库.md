[std::chrono - 搜索](https://cn.bing.com/search?q=std%3A%3Achrono&form=ANNTH1&refig=6a117394af3844439b7ca65e4ef403fb&pc=CNNDDB&adppc=EDGEDBB)


# C++ 时间库 `std::chrono` 笔记

`std::chrono` 是 C++11 引入的时间处理库，位于标准库的 `<chrono>` 头文件中。它提供了高精度、灵活且跨平台的工具，用于处理时间点、时间间隔以及与时钟相关的操作，是 C++ 标准库中时间相关操作的核心部分。

---

## 一、核心概念

表格

|概念|说明|
|---|---|
|**时间点 (Time Point)**|表示某个特定时刻，通常与某种时钟相关联。可通过 `std::chrono::system_clock::now()` 获取当前时间点。|
|**时间间隔 (Duration)**|表示两个时间点之间的时间差。`std::chrono::duration` 是模板类，可用秒、毫秒、微秒等单位表示时间长度。|
|**时钟 (Clock)**|时间点和时间间隔的来源，C++ 提供三种主要时钟类型：<br><br>1. `std::chrono::system_clock`：系统时钟，与系统时间同步。<br><br>2. `std::chrono::steady_clock`：单调时钟，不受系统时间调整影响。<br><br>3. `std::chrono::high_resolution_clock`：高分辨率时钟，提供最高精度。|

---

## 二、示例代码

### 1. 测量函数执行时间

cpp

运行

```
#include <iostream>
#include <chrono>
#include <thread>

void someFunction() {
    std::this_thread::sleep_for(std::chrono::seconds(1)); // 模拟耗时操作
}

int main() {
    // 记录开始时间点
    auto start = std::chrono::high_resolution_clock::now();
    
    someFunction(); // 执行待测量的函数
    
    // 记录结束时间点
    auto end = std::chrono::high_resolution_clock::now();
    
    // 计算时间差，并转换为毫秒
    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(end - start);
    
    std::cout << "Function took " << duration.count() << " milliseconds to execute." << std::endl;
    
    return 0;
}
```

### 2. 获取并格式化当前时间

cpp

运行

```
#include <iostream>
#include <chrono>
#include <ctime>

int main() {
    // 获取当前系统时间点
    auto now = std::chrono::system_clock::now();
    
    // 转换为 C 风格的 time_t 时间戳
    std::time_t now_c = std::chrono::system_clock::to_time_t(now);
    
    // 用 ctime 格式化为人类可读的字符串并输出
    std::cout << "Current date and time: " << std::ctime(&now_c);
    
    return 0;
}
```

---

## 三、应用场景

1. **测量代码执行时间**：通过 `steady_clock` 或 `high_resolution_clock` 测量代码块的运行时间。
2. **定时任务 / 延时操作**：使用 `std::this_thread::sleep_for` 实现延时、等待操作。
3. **跨平台时间处理**：`std::chrono` 提供统一的时间处理接口，适用于不同操作系统。

---

## 四、注意事项

- **时钟选择**：
    
    - `system_clock`：适合获取当前系统时间，可与 `time_t` 转换。
    - `steady_clock`：适合测量时间间隔（如代码耗时），不受系统时间调整影响。
    - `high_resolution_clock`：适合需要高精度的场景，通常是 `steady_clock` 的别名。
    
- **单位转换**：使用 `std::chrono::duration_cast` 在不同时间单位间转换，例如将秒转换为毫秒。

---

通过 `std::chrono`，C++ 提供了强大的时间处理能力，能够满足从简单时间操作到高精度测量的各种需求。