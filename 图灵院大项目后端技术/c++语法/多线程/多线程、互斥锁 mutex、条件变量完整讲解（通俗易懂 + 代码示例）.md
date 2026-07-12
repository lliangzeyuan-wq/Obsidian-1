
# 多线程、互斥锁 mutex、条件变量完整讲解（通俗易懂 + 代码示例）

## 一、基础：什么是多线程

### 1. 核心概念

进程：操作系统分配资源的最小单位（一个独立程序，如微信、浏览器）。 线程：进程内部**独立执行流**，共享进程的内存、全局变量、文件句柄等资源，是 CPU 调度最小单位。

- 单线程：代码从上到下串行执行，同一时间只能做一件事。
- 多线程：同一进程内多条执行流**并发运行**，同时执行多段代码，提升效率（文件读写、网络请求、计算任务）。

### 2. 多线程的致命问题：竞态条件（资源争抢）

多个线程**同时读写同一个共享变量 / 资源**时，CPU 切换线程会打断操作，导致数据错乱。 示例：两个线程同时对`int cnt=0`执行`cnt++`

```
// cnt++底层分三步：
1. 读取cnt的值到寄存器
2. 寄存器+1
3. 写回内存
```

线程 1 读到 0，还没写回，CPU 切到线程 2，线程 2 也读到 0，两个线程都 + 1 写回，最终结果`cnt=1`，预期应为 2，数据出错。 **解决工具：互斥锁 std::mutex**

---

## 二、互斥锁 std::mutex（解决竞态条件）

### 1. 作用

同一时间**只允许一个线程持有锁**，锁住共享资源的读写代码块，其他线程执行到`lock()`会阻塞等待，直到锁释放。

### 2. 核心接口

表格

|函数|功能|
|---|---|
|`mutex.lock()`|加锁：抢到锁继续执行；没抢到则阻塞休眠|
|`mutex.unlock()`|释放锁，其他线程可竞争获取|
|`std::lock_guard<std::mutex>`|自动锁：RAII 机制，离开作用域自动解锁，避免忘记 unlock|

### 3. 基础代码示例（修复 cnt 自增错乱）

```
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

int cnt = 0;
mutex mtx; // 全局互斥锁，保护共享变量cnt

void add_task() {
    for (int i = 0; i < 100000; i++) {
        lock_guard<mutex> guard(mtx); // 自动加锁，离开{}自动解锁
        cnt++; // 临界区：同一时间仅一个线程执行
    }
}

int main() {
    thread t1(add_task);
    thread t2(add_task);
    t1.join();
    t2.join();
    cout << cnt << endl; // 输出200000，数据正确
    return 0;
}
```

### 4. 关键特性

1. **排他性**：锁同一时间只能被一个线程占有；
2. **临界区**：被锁包裹、操作共享资源的代码块，尽量短小；
3. `lock_guard`优势：异常 /return 退出函数时自动解锁，不会死锁。

### 5. 死锁（mutex 常见坑）

多个线程互相持有对方需要的锁，永久阻塞。 例：线程 1 持有锁 A 等锁 B，线程 2 持有锁 B 等锁 A。 规避：统一锁的获取顺序、减少嵌套锁。

---

## 三、条件变量 std::condition_variable（线程等待 / 唤醒）

### 1. 解决什么问题？

单纯 mutex 只能 “锁住资源”，无法实现**线程等待条件、满足条件再唤醒**的场景。 经典场景：生产者 - 消费者模型

- 生产者：往队列放数据，队列满则等待；
- 消费者：从队列取数据，队列为空则等待；

如果只用 mutex：消费者不断循环加锁解锁判断队列，**忙等**，浪费 CPU。 条件变量：线程不满足条件时主动休眠，收到唤醒信号后再检查，无 CPU 空耗。

### 2. 配套依赖

条件变量**必须搭配 std::mutex 使用**，等待时会自动释放锁，唤醒后重新获取锁。

### 3. 核心接口

表格

|函数|作用|
|---|---|
|`cv.wait(lck, 条件判断)`|释放锁并阻塞休眠；收到唤醒后重新拿锁，再判断条件，不满足继续等|
|`cv.notify_one()`|唤醒**一个**正在等待的线程|
|`cv.notify_all()`|唤醒**全部**等待中的线程|

### 4. 完整生产者消费者代码示例

```
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>
using namespace std;

queue<int> data_queue;
mutex mtx;
condition_variable cv;
const int MAX_SIZE = 5; // 队列最大容量

// 生产者：生产数字放入队列
void producer() {
    for (int num = 1; num <= 10; num++) {
        unique_lock<mutex> lck(mtx);
        // 队列满，等待消费者取走数据
        cv.wait(lck, [](){ return data_queue.size() < MAX_SIZE; });
        
        data_queue.push(num);
        cout << "生产：" << num << endl;
        cv.notify_one(); // 唤醒一个等待的消费者
    }
}

// 消费者：取出队列数字
void consumer() {
    while (true) {
        unique_lock<mutex> lck(mtx);
        // 队列为空，等待生产者放入数据
        cv.wait(lck, [](){ return !data_queue.empty(); });

        int val = data_queue.front();
        data_queue.pop();
        cout << "消费：" << val << endl;
        cv.notify_one(); // 唤醒生产者
    }
}

int main() {
    thread t_prod(producer);
    thread t_cons(consumer);
    t_prod.join();
    t_cons.detach();
    return 0;
}
```

### 5. wait () 底层逻辑（重点）

1. 传入`unique_lock`，自动`unlock()`释放 mutex，其他线程可以操作共享队列；
2. 线程进入阻塞休眠，不占用 CPU；
3. 收到`notify_one/all`唤醒信号；
4. 自动重新`lock()`获取互斥锁；
5. 执行第二个参数的条件判断：
    - 条件成立：跳出 wait，执行后续代码；
    - 条件不成立：再次释放锁，继续等待（防止**虚假唤醒**）。

### 6. 虚假唤醒

操作系统可能无理由唤醒 wait 中的线程（硬件 / 调度机制导致），因此`wait`**必须带条件判断**，不能只写`cv.wait(lck)`。

---

## 四、mutex 和 condition_variable 对比总结

1. **std::mutex** 作用：保护共享资源，保证临界区代码同一时间单线程执行，解决数据竞争。 局限：无法实现线程休眠等待，只能阻塞争抢锁，会出现忙等浪费 CPU。
2. **std::condition_variable** 作用：线程间同步通信，让线程满足条件才执行，不满足则休眠。 依赖：必须绑定 mutex 使用，自身无法单独保护共享变量。

## 五、三者完整逻辑链路（多线程 + 锁 + 条件变量）

1. 多线程并发访问共享资源，产生数据错乱风险；
2. mutex 互斥锁保证同一时间仅一线程操作资源，消除竞态；
3. 条件变量基于 mutex 实现线程等待 / 唤醒，优化 CPU，实现生产者、消费者、任务池等同步场景。