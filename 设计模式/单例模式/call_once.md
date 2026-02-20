---
data: 2026-02-20
---
### 定义
`std::call_once` 是 C++11 引入的线程同步工具，定义在 `<mutex>` 头文件中，核心作用是：**无论多少个线程调用，传入的目标函数只会被执行一次**，且能避免多线程竞争导致的未定义行为。

它需要配合 `std::once_flag` 使用：

- `std::once_flag`：一个标记对象，用于记录目标函数是否已执行，**必须是全局 / 静态 / 成员变量（生命周期长）**，且不能被拷贝 / 移动。
- `std::call_once(flag, func, args...)`：第一个参数是 `once_flag`，第二个是要执行的函数，后续是函数的参数。

### 基础用法
下面是一个简单的多线程示例，演示 `call_once` 如何保证初始化函数只执行一次：

cpp

运行

```cpp
#include <iostream>
#include <thread>
#include <mutex>  // 必须包含此头文件

// 1. 定义 once_flag 标记（全局/静态，确保所有线程共享）
std::once_flag init_flag;

// 2. 要确保只执行一次的初始化函数
void init_resource() {
    std::cout << "初始化资源：这个函数只会执行一次！" << std::endl;
    // 这里可以放初始化代码（如创建单例、加载配置、初始化全局变量等）
}

// 3. 线程执行的函数
void thread_func(int id) {
    std::cout << "线程 " << id << " 尝试调用初始化函数..." << std::endl;
    // 4. 调用 call_once，保证 init_resource 只执行一次
    std::call_once(init_flag, init_resource);
    std::cout << "线程 " << id << " 初始化完成（实际可能未执行初始化）" << std::endl;
}

int main() {
    // 创建5个线程，都尝试调用 init_resource
    std::thread t1(thread_func, 1);
    std::thread t2(thread_func, 2);
    std::thread t3(thread_func, 3);
    std::thread t4(thread_func, 4);
    std::thread t5(thread_func, 5);

    // 等待所有线程结束
    t1.join();
    t2.join();
    t3.join();
    t4.join();
    t5.join();

    return 0;
}
```

#### 输出示例（顺序可能因线程调度不同，但核心不变）：

plaintext

```cpp
线程 1 尝试调用初始化函数...
初始化资源：这个函数只会执行一次！
线程 1 初始化完成（实际可能未执行初始化）
线程 2 尝试调用初始化函数...
线程 2 初始化完成（实际可能未执行初始化）
线程 3 尝试调用初始化函数...
线程 3 初始化完成（实际可能未执行初始化）
线程 4 尝试调用初始化函数...
线程 4 初始化完成（实际可能未执行初始化）
线程 5 尝试调用初始化函数...
线程 5 初始化完成（实际可能未执行初始化）
```



### 进阶用法：带参数的函数
`call_once`支持给目标函数传参，示例如下：
```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <string>

std::once_flag flag;

// 带参数的初始化函数
void init_with_args(const std::string& msg, int num) {
    std::cout << "带参数初始化：" << msg << "，数字：" << num << std::endl;
}

void worker(int id) {
    std::call_once(flag, init_with_args, "初始化配置", 100);
    std::cout << "工作线程 " << id << " 运行中" << std::endl;
}

int main() {
    std::thread t1(worker, 1);
    std::thread t2(worker, 2);
    t1.join();
    t2.join();
    return 0;
}
```

#### 输出：

plaintext

```cpp
带参数初始化：初始化配置，数字：100
工作线程 1 运行中
工作线程 2 运行中
```

