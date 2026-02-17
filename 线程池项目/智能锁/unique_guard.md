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

### unique_lock
#### unique_lock的构造
```cpp
std::mutex mtx;
std::unique_lock<std::mutex> lk1(mtx);//自动锁，和lock_guard的作用一样
std::unique_lock<std::mutex> lk1(mtx,std::defer_lock);//需要自己手动的上锁，但他会自动在他声明周期结束的时候解锁，也可以手动解锁
std::unique_lock<std::mutex> lk1(mtx,std::try_to_lock);//尝试上锁
std::unique_lock<std::mutex> lk1(mtx,std::adopt_lock);//接管已经上的锁
```
##### std::unique_lock 提供了一些成员函数，用于管理锁的状态：
- **lock()** ：锁定关联的 mutex。
- **unlock()**：解锁关联的 mutex。
- **try_lock()**：尝试锁定mutex，如果锁定成功，返回true，否则返回false。
- **owns_lock()**：返回一个布尔值，指示 unique_lock  是否拥有 mutex 的所用权。



#### defer_lock
```cpp
#include <iostream>
#include <thread>
#include <mutex>
std::mutex mtx;
int count = 0;
void example_defer_lock() {
    std::unique_lock<std::mutex>lock(mtx, std::defer_lock);//不会立即锁定，需要手动上锁
    lock.lock();//和mtx.lock(); 的作用一样，需要手动的上锁和解锁
    for (size_t i = 0; i <100000; i++)
    {
        count++;
    }
    lock.unlock();//这句可以省，因unique_lock会自动解锁
}
int main() {
    std::thread t1(example_defer_lock);
    std::thread t2(example_defer_lock);
    t1.join();
    t2.join();
    std::cout << count << std::endl;
    return 0;
}
```

#### try_to_lock
- 当unique_lock 尝试去锁的时候，mtx已经上锁了，因此检测的没有上锁
```cpp
#include <iostream>
#include <thread>
#include <mutex>
std::mutex mtx;
void example_try_to_lock() {
    mtx.lock(); 
    std::unique_lock<std::mutex>lock(mtx, std::try_to_lock);//尝试锁定
    if (lock.owns_lock()) {
        std::cout << "锁上了" << std::endl;
    }
    else {
        std::cout << "没锁上" << std::endl;
    }   
    mtx.unlock();
}
int main() {
    std::thread t1(example_try_to_lock);
    t1.join();
    return 0;
}
```


