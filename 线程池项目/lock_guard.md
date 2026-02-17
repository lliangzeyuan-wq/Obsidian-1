---
data: 2026-02-17
---
- `lock_guard` 是一个模板类，位于 `<mutex>` 头文件中。它符合 **RAII** 风格，主要用于管理 `mutex` 的生命周期，确保 `mutex` 在锁定的作用域内被正确地上锁和解锁。（作用和智能指针类似，避免忘记解锁）


- 使用普通锁的代码
```cpp
#include<iostream>
#include<thread>
#include<vector>
#include<mutex>
using namespace std;
mutex mymutex;
int mycount = 0;
void sum() {
	mymutex.lock();
	for (size_t i = 0; i < 10000; i++) {
		mycount++;
	}
	mymutex.unlock();

}
int main() {
	vector<thread>mybox;
	for (size_t i = 0; i < 10; i++)
	{
		mybox.emplace_back(sum);
	}    
	for (thread& t : mybox) {
		t.join();
	}
	cout << mycount << endl;
	return 0;
}
```