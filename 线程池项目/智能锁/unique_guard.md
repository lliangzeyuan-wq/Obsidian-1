---
data: 2026-02-18
---
- lock_guard的缺陷
 ```cpp
 #include <iostream>
#include <thread>
#include <mutex>
#include<vector>
std::mutex mymutex;
int mycount = 0;
int mycount1 = 0;

void sum() {
    // lock_guard 在构造时自动锁定互斥量
    {
        std::lock_guard<std::mutex> lock(mymutex);

        // 第一个循环：对 mycount 进行 10000 次自增
        for (size_t i = 0; i < 1000000; ++i) {
            mycount++;
        }
    }
    // 第二个循环：对 mycount1 进行 10000 次自增
    // 注意：整个函数都在互斥锁的保护下，直到 lock_guard 生命周期结束
    for (size_t i = 0; i < 1000000; ++i) {
        mycount1++;
    }

    // lock_guard 对象在函数结束时销毁，自动解锁互斥量
}

int main() {
    std::vector<std::thread>mybox;
    for (size_t i = 0; i < 10; i++)
    { 
        mybox.emplace_back(sum);
    }
    for (std::thread& t : mybox) {
        t.join();
    }

    std::cout << "mycount: " << mycount << std::endl;
    std::cout << "mycount1: " << mycount1 << std::endl;
    return 0;
}
 ```
- 注意sum函数下面的lock_guard，他如果想要只对第一个for循环上锁，需要加上一个大括号来限定他的作用范围。说明他不能自由的指定解锁的范围