我给你这份**最终上线版服务端代码**加上**逐行 / 逐模块超详细注释**，完全适配课程设计、答辩讲解，所有核心逻辑（永久申请、离线推送、好友校验、多线程）都标注清楚，你直接复制使用即可：

cpp

运行

```
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <pthread.h>

// 服务器监听端口号
#define SERV_PORT 9527
// 最大支持在线用户数
#define MAX_USER 100

// -------------------------- 数据结构体定义 --------------------------
// 在线用户结构体：存储用户学号和对应的socket文件描述符
typedef struct {
    char id[20];     // 用户唯一标识：学号
    int fd;          // 客户端连接的socket描述符，用于收发消息
} User;

// 好友关系结构体：永久存储双向好友关系
typedef struct {
    char a[20];      // 好友一方的学号
    char b[20];      // 好友另一方的学号
} FriendRelation;

// 离线好友申请结构体：存储未处理的好友申请（永久保存）
typedef struct {
    char from[20];   // 申请人的学号
    char to[20];     // 被申请人的学号
} OfflineRequest;

// -------------------------- 全局数据（内存存储） --------------------------
// 好友关系数组：存储所有好友对
FriendRelation friend_list[MAX_USER * 2];
// 好友关系总数
int friend_count = 0;

// 好友申请数组：存储所有未处理的申请
OfflineRequest request_list[MAX_USER * 2];
// 申请总数
int request_count = 0;

// 在线用户数组：存储当前所有在线的同学
User user_list[MAX_USER];
// 在线用户总数
int user_count = 0;

// 线程互斥锁：防止多线程同时修改全局数据，保证线程安全
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

// -------------------------- 函数声明 --------------------------
// 处理客户端连接的线程函数
void* handle_client(void* arg);
// 错误处理函数
void sys_err(const char* str);
// 根据用户学号查找在线socket描述符
int find_user_fd(const char* id);
// 添加好友关系（内存+文件持久化）
void add_friend(const char* id1, const char* id2);
// 删除好友关系（内存+文件持久化）
void del_friend(const char* id1, const char* id2);
// 判断两个用户是否是好友
int is_friend(const char* id1, const char* id2);
// 从文件加载历史好友数据
void load_friends();
// 保存好友数据到文件
void save_friends();
// 从文件加载历史好友申请数据
void load_requests();
// 保存好友申请数据到文件
void save_requests();
// 用户上线时，推送所有未处理的好友申请
void send_offline_requests(int cfd, const char* user_id);
// 删除已处理的好友申请（同意/拒绝后调用）
void remove_request(const char* from, const char* to);

// -------------------------- 主函数：服务器入口 --------------------------
int main() {
    // 1. 服务器启动时，加载本地文件的好友+申请数据（持久化核心）
    load_friends();
    load_requests();

    int lfd;                              // 监听socket描述符
    struct sockaddr_in serv_addr;         // 服务器地址结构体
    socklen_t clie_addr_len = sizeof(serv_addr);
    pthread_t tid;                        // 线程ID

    // 2. 创建TCP套接字
    lfd = socket(AF_INET, SOCK_STREAM, 0);
    if (lfd == -1) sys_err("socket创建失败");

    // 3. 设置端口复用，防止服务器重启报错
    int opt = 1;
    setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

    // 4. 绑定IP和端口
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(SERV_PORT);
    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
    if (bind(lfd, (struct sockaddr*)&serv_addr, sizeof(serv_addr)) == -1)
        sys_err("bind绑定失败");

    // 5. 开始监听客户端连接
    if (listen(lfd, 128) == -1) sys_err("listen监听失败");
    std::cout << "✅ 服务器启动成功！数据加载完成！\n" << std::endl;

    // 6. 循环接收客户端连接（多线程核心）
    while (1) {
        // 为新连接分配socket描述符
        int* cfd_ptr = new int;
        *cfd_ptr = accept(lfd, (struct sockaddr*)&serv_addr, &clie_addr_len);
        if (*cfd_ptr == -1) { delete cfd_ptr; continue; }

        // 创建新线程处理该客户端，实现多人同时在线
        pthread_create(&tid, NULL, handle_client, (void*)cfd_ptr);
        // 线程分离：自动释放资源，无需主线程等待
        pthread_detach(tid);
    }

    close(lfd);
    return 0;
}

// -------------------------- 功能函数实现 --------------------------
/**
 * @brief 判断两个用户是否为双向好友
 * @param id1 用户1学号
 * @param id2 用户2学号
 * @return 1=是好友 0=不是好友
 */
int is_friend(const char* id1, const char* id2) {
    pthread_mutex_lock(&mutex);  // 加锁：线程安全
    for (int i = 0; i < friend_count; i++) {
        // 双向判断：a和b 或 b和a 都算好友
        if ((strcmp(friend_list[i].a, id1) == 0 && strcmp(friend_list[i].b, id2) == 0) ||
            (strcmp(friend_list[i].a, id2) == 0 && strcmp(friend_list[i].b, id1) == 0)) {
            pthread_mutex_unlock(&mutex);
            return 1;
        }
    }
    pthread_mutex_unlock(&mutex);
    return 0;
}

/**
 * @brief 从friends.txt加载所有好友数据
 */
void load_friends() {
    pthread_mutex_lock(&mutex);
    // 打开文件：不存在则创建
    FILE* fp = fopen("friends.txt", "a+");
    rewind(fp);  // 指针移到文件开头

    char a[20], b[20];
    // 逐行读取好友对
    while (fscanf(fp, "%s %s", a, b) != EOF) {
        strcpy(friend_list[friend_count].a, a);
        strcpy(friend_list[friend_count].b, b);
        friend_count++;
    }
    fclose(fp);
    pthread_mutex_unlock(&mutex);
}

/**
 * @brief 保存好友数据到friends.txt
 */
void save_friends() {
    pthread_mutex_lock(&mutex);
    FILE* fp = fopen("friends.txt", "w");
    // 写入所有好友关系
    for (int i = 0; i < friend_count; i++)
        fprintf(fp, "%s %s\n", friend_list[i].a, friend_list[i].b);
    fclose(fp);
    pthread_mutex_unlock(&mutex);
}

/**
 * @brief 从requests.txt加载所有未处理的好友申请
 */
void load_requests() {
    pthread_mutex_lock(&mutex);
    FILE* fp = fopen("requests.txt", "a+");
    rewind(fp);

    char f[20], t[20];
    // 逐行读取申请
    while (fscanf(fp, "%s %s", f, t) != EOF) {
        strcpy(request_list[request_count].from, f);
        strcpy(request_list[request_count].to, t);
        request_count++;
    }
    fclose(fp);
    pthread_mutex_unlock(&mutex);
}

/**
 * @brief 保存好友申请到requests.txt
 */
void save_requests() {
    pthread_mutex_lock(&mutex);
    FILE* fp = fopen("requests.txt", "w");
    for (int i = 0; i < request_count; i++)
        fprintf(fp, "%s %s\n", request_list[i].from, request_list[i].to);
    fclose(fp);
    pthread_mutex_unlock(&mutex);
}

/**
 * @brief 添加好友（永久存储）
 */
void add_friend(const char* id1, const char* id2) {
    pthread_mutex_lock(&mutex);
    strcpy(friend_list[friend_count].a, id1);
    strcpy(friend_list[friend_count].b, id2);
    friend_count++;
    pthread_mutex_unlock(&mutex);
    save_friends();  // 同步写入文件
}

/**
 * @brief 删除好友（永久删除）
 */
void del_friend(const char* id1, const char* id2) {
    pthread_mutex_lock(&mutex);
    for (int i = 0; i < friend_count; i++) {
        if ((strcmp(friend_list[i].a, id1) == 0 && strcmp(friend_list[i].b, id2) == 0) ||
            (strcmp(friend_list[i].a, id2) == 0 && strcmp(friend_list[i].b, id1) == 0)) {
            // 数组前移覆盖，删除该好友关系
            for (int j = i; j < friend_count - 1; j++)
                friend_list[j] = friend_list[j + 1];
            friend_count--;
            break;
        }
    }
    pthread_mutex_unlock(&mutex);
    save_friends();
}

/**
 * @brief 根据学号查找在线用户的socket描述符
 * @return 在线返回fd，不在线返回-1
 */
int find_user_fd(const char* id) {
    int fd = -1;
    pthread_mutex_lock(&mutex);
    for (int i = 0; i < user_count; i++) {
        if (strcmp(user_list[i].id, id) == 0) {
            fd = user_list[i].fd;
            break;
        }
    }
    pthread_mutex_unlock(&mutex);
    return fd;
}

/**
 * @brief 用户上线时，推送所有未处理的好友申请
 * 核心修复：只推送，不删除申请，申请永久留存直到处理
 */
void send_offline_requests(int cfd, const char* user_id) {
    pthread_mutex_lock(&mutex);
    for (int i = 0; i < request_count; i++) {
        // 找到发送给当前用户的所有申请
        if (strcmp(request_list[i].to, user_id) == 0) {
            char msg[256];
            snprintf(msg, sizeof(msg), "📩 未处理申请：用户%s想加你为好友！回复@%s:ACCEPT同意", request_list[i].from, request_list[i].from);
            write(cfd, msg, strlen(msg));
        }
    }
    pthread_mutex_unlock(&mutex);
}

/**
 * @brief 删除已处理的好友申请（同意/拒绝后调用）
 */
void remove_request(const char* from, const char* to) {
    pthread_mutex_lock(&mutex);
    for (int i = 0; i < request_count; i++) {
        // 匹配申请人和被申请人
        if (strcmp(request_list[i].from, from) == 0 && strcmp(request_list[i].to, to) == 0) {
            // 数组前移覆盖，删除该申请
            for (int j = i; j < request_count - 1; j++) {
                request_list[j] = request_list[j + 1];
            }
            request_count--;
            break;
        }
    }
    pthread_mutex_unlock(&mutex);
    save_requests();  // 同步更新文件
}

// -------------------------- 客户端处理线程函数（核心业务） --------------------------
void* handle_client(void* arg) {
    int cfd = *(int*)arg;   // 客户端socket描述符
    delete (int*)arg;       // 释放堆内存
    char buf[1024], user_id[20];  // 接收缓冲区、当前用户学号
    int n;

    // 1. 接收客户端发送的学号（登录）
    memset(buf, 0, sizeof(buf));
    n = read(cfd, buf, sizeof(buf));
    if (n <= 0) { close(cfd); return NULL; }
    strcpy(user_id, buf);
    std::cout << "✅ 用户" << user_id << "上线\n";

    // 2. 将用户加入在线列表
    pthread_mutex_lock(&mutex);
    strcpy(user_list[user_count].id, user_id);
    user_list[user_count].fd = cfd;
    user_count++;
    pthread_mutex_unlock(&mutex);

    // 3. 上线推送：所有未处理的好友申请（核心功能）
    send_offline_requests(cfd, user_id);

    // 4. 循环处理客户端指令
    while (1) {
        memset(buf, 0, sizeof(buf));
        n = read(cfd, buf, sizeof(buf));
        if (n <= 0) break;  // 客户端断开连接

        std::cout << "用户" << user_id << "：" << buf << std::endl;

        char target_id[20] = { 0 }, cmd[32] = { 0 };
        // 解析指令格式：@目标学号:指令/消息
        if (buf[0] == '@') {
            char* p = strchr(buf, ':');
            if (p) {
                *p = '\0';
                strcpy(target_id, buf + 1);  // 目标用户学号
                strcpy(cmd, p + 1);         // 指令/消息内容

                // ============== 指令1：发送好友申请 MATCH ==============
                if (strcmp(cmd, "MATCH") == 0) {
                    // 永久保存申请到文件（无论对方是否在线）
                    pthread_mutex_lock(&mutex);
                    strcpy(request_list[request_count].from, user_id);
                    strcpy(request_list[request_count].to, target_id);
                    request_count++;
                    pthread_mutex_unlock(&mutex);
                    save_requests();

                    // 如果对方在线，实时推送新申请
                    int fd = find_user_fd(target_id);
                    if (fd != -1) {
                        char msg[256];
                        snprintf(msg, sizeof(msg), "📩 新申请：用户%s想加你为好友！回复@%s:ACCEPT同意", user_id, user_id);
                        write(fd, msg, strlen(msg));
                    }
                    write(cfd, "✅ 好友申请已发送！对方上线即可查看", 40);
                }
                // ============== 指令2：同意好友申请 ACCEPT ==============
                else if (strcmp(cmd, "ACCEPT") == 0) {
                    add_friend(user_id, target_id);                // 添加好友
                    remove_request(target_id, user_id);            // 删除已处理申请
                    write(cfd, "✅ 同意成功！你们已是好友！", 30);
                }
                // ============== 指令3：拒绝好友申请 REJECT ==============
                else if (strcmp(cmd, "REJECT") == 0) {
                    remove_request(target_id, user_id);            // 删除申请
                    write(cfd, "❌ 已拒绝该好友申请", 25);
                }
                // ============== 指令4：删除好友 DEL ==============
                else if (strcmp(cmd, "DEL") == 0) {
                    del_friend(user_id, target_id);
                    write(cfd, "🗑️ 删除好友成功！", 20);
                }
                // ============== 指令5：好友聊天消息 ==============
                else {
                    // 非好友禁止发消息（权限校验）
                    if (!is_friend(user_id, target_id)) {
                        write(cfd, "❌ 你们还不是好友，无法发送消息！", 40);
                        continue;
                    }
                    // 转发消息给目标用户
                    int fd = find_user_fd(target_id);
                    if (fd != -1) {
                        char msg[1024];
                        snprintf(msg, sizeof(msg), "[%s]: %s", user_id, cmd);
                        write(fd, msg, strlen(msg));
                    } else {
                        write(cfd, "❌ 对方不在线", 20);
                    }
                }
            }
            else write(cfd, "❌ 格式错误", 15);
        }
        else write(cfd, "❌ 格式错误", 15);
    }

    // 5. 用户下线：从在线列表移除
    pthread_mutex_lock(&mutex);
    for (int i = 0; i < user_count; i++) {
        if (user_list[i].fd == cfd) {
            for (int j = i; j < user_count - 1; j++)
                user_list[j] = user_list[j + 1];
            user_count--;
            break;
        }
    }
    pthread_mutex_unlock(&mutex);
    close(cfd);
    std::cout << "❌ 用户" << user_id << "下线\n";
    return NULL;
}

// 错误处理函数
void sys_err(const char* str) {
    perror(str);
    exit(1);
}
```

---

# 核心注释标注的关键特性（满足你的上线需求）

1. **永久好友申请**
    
    所有申请都会存入 `requests.txt`，**不处理永远不删除**，重启服务器 / 上下线都保留
2. **上线推送**
    
    用户登录后，自动收到所有**未处理的申请**，不会丢失
3. **好友权限校验**
    
    非好友无法发送消息，完全符合社交软件逻辑