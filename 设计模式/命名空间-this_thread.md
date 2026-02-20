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


```cpp
#include <iostream>
#include <thread>
#include <chrono>
using namespace std;

void func()
{
    for (int i = 0; i < 10; ++i)
    {
        // 获取当前系统时间点
        auto now = chrono::system_clock::now();
        // 时间间隔为2s
        chrono::seconds sec(2);
        // 当前时间点之后休眠两秒
        this_thread::sleep_until(now + sec);
        cout << "子线程: " << this_thread::get_id() << ", i = " << i << endl;
    }
}

int main()
{
    thread t(func);
    t.join();
}
```
sleep_until()和sleep_for()函数的功能是一样的，只不过前者是基于时间点去阻塞线程，后者是基于时间段去阻塞线程，项目开发过程中根据实际情况选择最优的解决方案即可。



# 4. yield()
命名空间this_thread中提供了一个非常绅士的函数yield()，在线程中调用这个函数之后，处于运行态的线程会主动让出自己已经抢到的CPU时间片，最终变为就绪态，这样其它的线程就有更大的概率能够抢到CPU时间片了。使用这个函数的时候需要注意一点，线程调用了yield()之后会主动放弃CPU资源，但是这个变为就绪态的线程会马上参与到下一轮CPU的抢夺战中，不排除它能继续抢到CPU时间片的情况，这是概率问题。


```cpp
#include <iostream>
#include <thread>
using namespace std;

void func()
{
    for (int i = 0; i < 100000000000; ++i)
    {
        cout << "子线程: " << this_thread::get_id() << ", i = " << i << endl;
        this_thread::yield();
    }
}

int main()
{
    thread t(func);
    thread t1(func);
    t.join();
    t1.join();
}
```
在上面的程序中，执行func()中的for循环会占用大量的时间，在极端情况下，如果当前线程占用CPU资源不释放就会导致其他线程中的任务无法被处理，或者该线程每次都能抢到CPU时间片，导致其他线程中的任务没有机会被执行。解决方案就是每执行一次循环，让该线程主动放弃CPU资源，重新和其他线程再次抢夺CPU时间片，如果其他线程抢到了CPU时间片就可以执行相应的任务了。

>结论：
std::this_thread::yield() 的目的是避免一个线程长时间占用CPU资源，从而导致多线程处理性能下降
std::this_thread::yield() 是让当前线程主动放弃了当前自己抢到的CPU资源，但是在下一轮还会继续抢
