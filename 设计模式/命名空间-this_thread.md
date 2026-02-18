---
data: 2026-02-18
---
[命名空间 - this_thread | 爱编程的大丙](https://subingwen.cn/cpp/this_thread/)
- 这个链接里面讲的非常详细，我就简单记一下，详细的到这个链接里面找

# 1. get_id()
调用命名空间std::this_thread中的get_id()方法可以得到当前线程的线程ID

```cpp
#include <iostream>
#include <thread>
using namespace std;

void func()
{
    cout << "子线程: " << this_thread::get_id() << endl;
}

int main()
{
    cout << "主线程: " << this_thread::get_id() << endl;
    thread t(func);
    t.join();
}
```
程序启动，开始执行main()函数，此时只有一个线程也就是主线程。当创建了子线程对象t之后，指定的函数func()会在子线程中执行，这时通过调用this_thread::get_id()就可以得到当前线程的线程ID了。



# 2. sleep_for()
```cpp
#include <iostream>
#include <thread>
#include <chrono>
using namespace std;

void func()
{
    for (int i = 0; i < 10; ++i)
    {
        this_thread::sleep_for(chrono::seconds(1));
        cout << "子线程: " << this_thread::get_id() << ", i = " << i << endl;
    }
}

int main()
{
    thread t(func);
    t.join();
}
```
在func()函数的for循环中使用了this_thread::sleep_for(chrono::seconds(1));之后，每循环一次程序都会阻塞1秒钟，也就是说每隔1秒才会进行一次输出。需要注意的是：程序休眠完成之后，会从阻塞态重新变成就绪态，就绪态的线程需要再次争抢CPU时间片，抢到之后才会变成运行态，这时候程序才会继续向下运行。

# 3. sleep_until()
命名空间this_thread中提供了另一个休眠函数sleep_until()，和sleep_for()不同的是它的参数类型不一样

sleep_until()：指定线程阻塞到某一个指定的时间点time_point类型，之后解除阻塞
sleep_for()：指定线程阻塞一定的时间长度duration 类型，之后解除阻塞


