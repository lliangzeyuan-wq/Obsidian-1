---
data: 2026-03-12
---
### 🎯 学习路线规划（针对 C++ WebServer 项目）

你现在看的是黑马的 **Linux 系统编程** 和 **Linux 网络编程** 两门课，我帮你规划到刚好能支撑写 WebServer 的程度：

---

#### 1️⃣ 先看完《Linux 系统编程》

**目标**：掌握进程、线程、同步互斥、IO、信号等基础，为并发模型打基础。

**建议看到这些核心章节为止**：

- 文件 IO（open/read/write/close、lseek）
- 进程控制（fork、exec、wait）
- 线程（pthread_create、线程同步）
    
    - 互斥锁 `pthread_mutex_t`
    - 条件变量 `pthread_cond_t`（你截图里正在学的部分）
    - 信号量 `sem_t`
    
- 信号处理（signal、sigaction）
- 进程间通信（管道、共享内存、消息队列，了解即可）
- 多路复用基础（select/poll/epoll，这部分会在网络编程里深入）

> ✅ 学到这里就够了，后面的高级主题（如内存池、日志系统）可以在写 WebServer 时再补。

---

#### 2️⃣ 再看完《Linux 网络编程》

**目标**：掌握 Socket 编程、TCP/IP、HTTP，这是 WebServer 的核心。

**建议看到这些核心章节为止**：

- TCP/IP 模型、四层 / 七层模型
- Socket API 全流程：`socket` → `bind` → `listen` → `accept` → `read/write`
- IO 多路复用：**epoll（重点）**
    
    - LT/ET 模式
    - `epoll_create`/`epoll_ctl`/`epoll_wait`
    
- HTTP 协议：
    
    - 请求 / 响应格式
    - 状态码、请求头、响应头
    
- 简单的 Web 服务器 demo（课程里一般会有小案例）

> ✅ 学到这里，你已经具备写一个 **基础版 C++ WebServer** 的全部知识了。

---

### 📌 总结：看到哪里算够？

- **《Linux 系统编程》**：看到 **线程同步（互斥锁 + 条件变量 + 信号量）+ 信号 + 进程间通信基础** 就可以停。
- **《Linux 网络编程》**：看到 **epoll 多路复用 + HTTP 协议 + 简单 Web 服务器示例** 就可以停。

之后就可以直接上手写 WebServer 项目，遇到具体问题（比如线程池、定时器、日志）再回头补对应的知识点。

---

### 💡 下一步建议

如果你现在正在学**条件变量**，建议先把系统编程里的**线程同步部分看完**，再去学网络编程的 **epoll 和 HTTP**。

要不要我帮你整理一份**从这两门课到写出 WebServer 的逐天学习计划**？你可以直接照着执行。