---
data: 2026-02-20
---
### 什么是条件变量
条件变量（condition variable）是一种同步机制，用于线程间通信。它允许一个线程等待另一个线程完成某项工作，然后再继续执行。条件变量提供了一种线程间通信的机制，使得线程可以等待某个条件的发生，然后再从等待的状态中恢复。

由此可见，条件变量在**生产者-消费者模型**当中有用武之地。
## 条件变量的基本用法

- 头文件 `#include<condition_variable>`
- 创建一个条件变量`std::condition_variable cv;`。
- 1. `notify_one()`：唤醒一个等待线程。
    
2. `notify_all()`：唤醒所有等待线程。这个一般用的比`notify_one()`多得多。
    
    > 适用场景: 生产者-消费者模型中，消费者将所有资源都消费完了，因此消费者的消费行为应该被阻塞，直到生产者生产出东西供其消费。而生产者生产完东西后，往往消费者需要一段时间才能消费，因此需要生产者通知消费者可以继续生产。
    
    > 由此可见，线程间的通信是必不可免的。
    
2. `wait(unique_lock<mutex>& lock, function<bool()> pred)`：等待一个条件，==直到== `pred` 返回 `true`。（条件为真醒来，条件为假睡眠（等待））
    
    > wait: 即一直等待，直到predict条件成立
    - wait作用的原理是解开锁，然后线程到睡眠状态，直到有人用`notify`把他唤醒，
    

### 代码例子
- `std::condition_variable cv;//111` 创建条件变量

- ```cpp
  		//while (q.size() > QUEUE_THRESHHOLD) {
		//	cv.wait(lock);
		//}
		//等价写法
		cv.wait(lock, [&]()->bool {return q.size() <= QUEUE_THRESHHOLD; });//333
  ```

- ```cpp
  //while (q.empty()) {
		//	cv.wait(lock);
		//}
		//等价写法
		cv.wait(lock, [&]()->bool {return !q.empty(); });//444
  ```

```cpp
#include<iostream>
#include<thread>
#include<mutex>
#include<condition_variable>
#include<queue>

const int QUEUE_THRESHHOLD = 10;
std::mutex mtx;
std::condition_variable cv;//111

class Queue {
public:
	void put(int val) {
		std::unique_lock<std::mutex>lock(mtx);
		//while (q.size() > QUEUE_THRESHHOLD) {
		//	cv.wait(lock);
		//}
		//等价写法
		cv.wait(lock, [&]()->bool {return q.size() <= QUEUE_THRESHHOLD; });//333
		
		
		q.push(val);
		std::cout << "producer:" << val << std::endl;
		cv.notify_all();
	}
	
	int get() {
		std::unique_lock<std::mutex>lock(mtx);
		//while (q.empty()) {
		//	cv.wait(lock);
		//}
		//等价写法
		cv.wait(lock, [&]()->bool {return !q.empty(); });//444
		
		
		int val = q.front();
		q.pop();
		std::cout << "consumer:" << val << std::endl;
		cv.notify_all();
		return val;
	}
private:
	std::queue<int>q;
};

void producer(Queue* q) {
	for (int i = 1; i <= 100; i++) {
		q->put(i);
		std::this_thread::sleep_for(std::chrono::milliseconds(1));
	}
}

void consumer(Queue* q) {
	for (int i = 1; i <= 100; i++) {
		q->get();
		std::this_thread::sleep_for(std::chrono::milliseconds(100));
	}
}
int main() {
	Queue q;
	std::thread t1(producer, &q);
	std::thread t2(consumer, &q);
	t1.join();
	t2.join();
}
```