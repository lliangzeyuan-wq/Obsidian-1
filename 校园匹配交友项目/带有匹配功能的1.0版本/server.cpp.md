// 标准C++头文件，兼容所有系统

#include <iostream>

#include <cstdio>

#include <cstdlib>

#include <cstring>

#include <unistd.h>

#include <sys/socket.h>

#include <netinet/in.h>

#include <pthread.h>

  

// 配置

#define SERV_PORT 9527

#define MAX_USER 100

#define USER_FILE "users.dat"

  

// 匹配模式枚举

typedef enum {

    MODE_NONE,        // 未选择模式

    MODE_STUDY,       // 1-学习搭子

    MODE_POSTGRAD,    // 2-考研搭子

    MODE_FRIEND,      // 3-兴趣交友

    MODE_LOVE         // 4-异性恋爱

} MatchMode;

  

// 学习搭子资料

typedef struct {

    char subject[50];

    char grade[20];

} StudyData;

  

// 考研搭子资料

typedef struct {

    char major[50];

    char target_school[50];

} PostgradData;

  

// 兴趣交友资料

typedef struct {

    char hobby[50];

    char personality[20];

} FriendData;

  

// 异性恋爱资料

typedef struct {

    char gender[10];

    char intro[100];

} LoveData;

  

// 用户结构体

typedef struct {

    char id[20];

    int fd;

    MatchMode current_mode;

    StudyData study_info;

    PostgradData postgrad_info;

    FriendData friend_info;

    LoveData love_info;

    char friends[MAX_USER][20];

    int friend_count;

} User;

  

// 全局在线用户列表

User user_list[MAX_USER];

int user_count = 0;

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

  

// 函数声明

void* handle_client(void* arg);

void sys_err(const char* str);

void match_user(int cfd, const char* self_id);

int load_users(User* all_users, int max_size);

void save_users(User* all_users, int count);

User* find_user_by_id(User* all_users, int all_count, const char* id);

int find_online_fd(const char* id);

  

// ==================== 【文件持久化】用户数据存储 ====================

// 从文件加载所有用户

int load_users(User* all_users, int max_size) {

    FILE* fp = fopen(USER_FILE, "rb");

    if (!fp) return 0;

    int count = 0;

    while (fread(&all_users[count], sizeof(User), 1, fp) == 1 && count < max_size) {

        count++;

    }

    fclose(fp);

    return count;

}

  

// 保存用户到文件

void save_users(User* all_users, int count) {

    FILE* fp = fopen(USER_FILE, "wb");

    if (!fp) return;

    fwrite(all_users, sizeof(User), count, fp);

    fclose(fp);

}

  

// 根据学号查找用户

User* find_user_by_id(User* all_users, int all_count, const char* id) {

    for (int i = 0; i < all_count; i++) {

        if (strcmp(all_users[i].id, id) == 0) {

            return &all_users[i];

        }

    }

    return NULL;

}

  

// 查询用户是否在线

int find_online_fd(const char* id) {

    pthread_mutex_lock(&mutex);

    int fd = -1;

    for (int i = 0; i < user_count; i++) {

        if (strcmp(user_list[i].id, id) == 0) {

            fd = user_list[i].fd;

            break;

        }

    }

    pthread_mutex_unlock(&mutex);

    return fd;

}

  

// ==================== 【核心】匹配算法（在线+离线+状态显示） ====================

void match_user(int cfd, const char* self_id) {

    // 加载所有注册用户（在线+离线）

    User all_users[MAX_USER];

    int all_count = load_users(all_users, MAX_USER);

  

    // 【修复】从内存获取当前在线用户信息，永不报错

    pthread_mutex_lock(&mutex);

    User* self = NULL;

    for (int i = 0; i < user_count; i++) {

        if (user_list[i].fd == cfd) {

            self = &user_list[i];

            break;

        }

    }

    if (!self || self->current_mode == MODE_NONE) {

        write(cfd, "❌ 请先选择匹配模式！", strlen("❌ 请先选择匹配模式！"));

        pthread_mutex_unlock(&mutex);

        return;

    }

    pthread_mutex_unlock(&mutex);

  

    int match_index = -1;

    int max_score = -1;

  

    // 遍历所有用户匹配

    for (int i = 0; i < all_count; i++) {

        User* target = &all_users[i];

        if (strcmp(target->id, self_id) == 0) continue;

        if (target->current_mode != self->current_mode) continue;

  

        int score = 0;

        // 加权评分匹配

        if (self->current_mode == MODE_STUDY) {

            if (strcmp(self->study_info.subject, target->study_info.subject) == 0) score += 40;

            if (strcmp(self->study_info.grade, target->study_info.grade) == 0) score += 30;

        }

        if (self->current_mode == MODE_POSTGRAD) {

            if (strcmp(self->postgrad_info.major, target->postgrad_info.major) == 0) score += 40;

            if (strcmp(self->postgrad_info.target_school, target->postgrad_info.target_school) == 0) score += 30;

        }

        if (self->current_mode == MODE_FRIEND) {

            if (strcmp(self->friend_info.hobby, target->friend_info.hobby) == 0) score += 50;

        }

        if (self->current_mode == MODE_LOVE) {

            if (strcmp(self->love_info.gender, target->love_info.gender) != 0) score += 50;

        }

  

        // 在线用户优先加分

        if (find_online_fd(target->id) != -1) score += 10;

  

        if (score > max_score) {

            max_score = score;

            match_index = i;

        }

    }

  

    char res[1024];

    if (match_index != -1) {

        User* mu = &all_users[match_index];

        const char* status = (find_online_fd(mu->id) != -1) ? "🟢 在线" : "🔴 离线";

        snprintf(res, sizeof(res), "✅ 匹配成功！用户：%s (%s)\n申请格式：@%s:REQUEST:想和你做搭子~",

            mu->id, status, mu->id);

    }

    else {

        strcpy(res, "❌ 暂无匹配用户！");

    }

    write(cfd, res, strlen(res));

}

  

// ==================== 主函数 ====================

int main() {

    int lfd;

    struct sockaddr_in serv_addr;

    serv_addr.sin_family = AF_INET;

    serv_addr.sin_port = htons(SERV_PORT);

    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);

    socklen_t clie_addr_len;

    pthread_t tid;

  

    lfd = socket(AF_INET, SOCK_STREAM, 0);

    if (lfd == -1) sys_err("socket创建失败");

  

    int opt = 1;

    setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

  

    if (bind(lfd, (struct sockaddr*)&serv_addr, sizeof(serv_addr)) == -1)

        sys_err("bind绑定失败");

  

    if (listen(lfd, 128) == -1) sys_err("listen监听失败");

    std::cout << "✅ 校园社交服务器启动成功！端口：" << SERV_PORT << std::endl;

  

    while (1) {

        int* cfd = new int;

        *cfd = accept(lfd, NULL, &clie_addr_len);

        if (*cfd == -1) { delete cfd; continue; }

        pthread_create(&tid, NULL, handle_client, cfd);

        pthread_detach(tid);

    }

    close(lfd);

    return 0;

}

  

// ==================== 客户端处理线程 ====================

void* handle_client(void* arg) {

    int cfd = *(int*)arg;

    delete (int*)arg;

  

    char buf[1024];

    int n;

    char user_id[20];

  

    // 登录：加载/创建用户

    memset(buf, 0, sizeof(buf));

    n = read(cfd, buf, sizeof(buf));

    if (n <= 0) { close(cfd); return NULL; }

    strcpy(user_id, buf);

  

    User all_users[MAX_USER];

    int all_count = load_users(all_users, MAX_USER);

    User* self = find_user_by_id(all_users, all_count, user_id);

  

    if (!self) {

        self = &all_users[all_count];

        strcpy(self->id, user_id);

        self->current_mode = MODE_NONE;

        self->friend_count = 0;

        all_count++;

        save_users(all_users, all_count);

    }

  

    // 添加到在线列表

    pthread_mutex_lock(&mutex);

    user_list[user_count] = *self;

    user_list[user_count].fd = cfd;

    user_count++;

    pthread_mutex_unlock(&mutex);

  

    // 模式选择

    while (1) {

        memset(buf, 0, sizeof(buf));

        n = read(cfd, buf, sizeof(buf));

        if (n <= 0) break;

  

        if (strstr(buf, "MODE:") != NULL) {

            int mode = atoi(buf + 5);

            pthread_mutex_lock(&mutex);

            for (int i = 0; i < user_count; i++) {

                if (user_list[i].fd == cfd) {

                    user_list[i].current_mode = (mode >= 1 && mode <= 4) ? (MatchMode)mode : MODE_NONE;

                    break;

                }

            }

            pthread_mutex_unlock(&mutex);

  

            char confirm[100];

            if (mode < 1 || mode>4) strcpy(confirm, "模式选择错误");

            else if (mode == 1) strcpy(confirm, "已选择【学习搭子】");

            else if (mode == 2) strcpy(confirm, "已选择【考研搭子】");

            else if (mode == 3) strcpy(confirm, "已选择【兴趣交友】");

            else strcpy(confirm, "已选择【异性恋爱】");

            write(cfd, confirm, strlen(confirm));

        }

        else if (strstr(buf, "STUDY:") != NULL) {

            char s[50], g[20];

            sscanf(buf + 6, "%[^|]|%s", s, g);

            pthread_mutex_lock(&mutex);

            for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { strcpy(user_list[i].study_info.subject, s);strcpy(user_list[i].study_info.grade, g);break; } }

            pthread_mutex_unlock(&mutex);

            write(cfd, "资料保存成功！", strlen("资料保存成功！"));

            break;

        }

        else if (strstr(buf, "POSTGRAD:") != NULL) {

            char m[50], sc[50];sscanf(buf + 9, "%[^|]|%s", m, sc);

            pthread_mutex_lock(&mutex);

            for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { strcpy(user_list[i].postgrad_info.major, m);strcpy(user_list[i].postgrad_info.target_school, sc);break; } }

            pthread_mutex_unlock(&mutex);write(cfd, "资料保存成功！", strlen("资料保存成功！"));break;

        }

        else if (strstr(buf, "FRIEND:") != NULL) {

            char h[50], p[20];sscanf(buf + 7, "%[^|]|%s", h, p);

            pthread_mutex_lock(&mutex);

            for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { strcpy(user_list[i].friend_info.hobby, h);strcpy(user_list[i].friend_info.personality, p);break; } }

            pthread_mutex_unlock(&mutex);write(cfd, "资料保存成功！", strlen("资料保存成功！"));break;

        }

        else if (strstr(buf, "LOVE:") != NULL) {

            char g[10], in[100];sscanf(buf + 5, "%[^|]|%s", g, in);

            pthread_mutex_lock(&mutex);

            for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { strcpy(user_list[i].love_info.gender, g);strcpy(user_list[i].love_info.intro, in);break; } }

            pthread_mutex_unlock(&mutex);write(cfd, "资料保存成功！", strlen("资料保存成功！"));break;

        }

    }

  

    // 消息处理

    while (1) {

        memset(buf, 0, sizeof(buf));

        n = read(cfd, buf, sizeof(buf));

        if (n <= 0) break;

  

        if (strcmp(buf, "MATCH") == 0) { match_user(cfd, user_id);continue; }

  

        char target_id[20] = { 0 };

        if (strstr(buf, "REQUEST:") != NULL) {

            char* p = strchr(buf, ':');

            if (p) {

                *p = '\0';strcpy(target_id, buf + 1);

                int fd = find_online_fd(target_id);

                char msg[1024];snprintf(msg, sizeof(msg), "📩 用户%s申请添加你为好友\n回复：AGREE:%s / REFUSE:%s", user_id, user_id, user_id);

                if (fd != -1)write(fd, msg, strlen(msg));

                write(cfd, "✅ 申请已发送！", strlen("✅ 申请已发送！"));

            }

        }

        else if (strstr(buf, "AGREE:") != NULL) {

            char sid[20];if (sscanf(buf, "AGREE:%[^:]", sid) != 1) { write(cfd, "❌ 格式：AGREE:学号", strlen("❌ 格式：AGREE:学号"));continue; }

            int fd = find_online_fd(sid);

            pthread_mutex_lock(&mutex);

            for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { strcpy(user_list[i].friends[user_list[i].friend_count++], sid); } }

            for (int i = 0;i < user_count;i++) { if (strcmp(user_list[i].id, sid) == 0) { strcpy(user_list[i].friends[user_list[i].friend_count++], user_id); } }

            pthread_mutex_unlock(&mutex);

            if (fd != -1)write(fd, "✅ 对方同意了你的申请！", strlen("✅ 对方同意了你的申请！"));

            write(cfd, "✅ 你已同意申请！", strlen("✅ 你已同意申请！"));

        }

        else if (strstr(buf, "REFUSE:") != NULL) {

            char sid[20];if (sscanf(buf, "REFUSE:%[^:]", sid) != 1) { write(cfd, "❌ 格式：REFUSE:学号", strlen("❌ 格式：REFUSE:学号"));continue; }

            int fd = find_online_fd(sid);

            if (fd != -1)write(fd, "❌ 对方拒绝了你的申请", strlen("❌ 对方拒绝了你的申请"));

            write(cfd, "✅ 已拒绝", strlen("✅ 已拒绝"));

        }

        else if (buf[0] == '@') {

            char* p = strchr(buf, ':');

            if (p) {

                *p = '\0';strcpy(target_id, buf + 1);bool ok = 0;

                pthread_mutex_lock(&mutex);

                for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { for (int j = 0;j < user_list[i].friend_count;j++) { if (strcmp(user_list[i].friends[j], target_id) == 0) { ok = 1;break; } }break; } }

                pthread_mutex_unlock(&mutex);

                if (!ok) { write(cfd, "❌ 你们还不是好友", strlen("❌ 你们还不是好友"));continue; }

                int fd = find_online_fd(target_id);

                if (fd == -1) { write(cfd, "❌ 对方不在线", strlen("❌ 对方不在线"));continue; }

                char msg[1024];snprintf(msg, sizeof(msg), "[%s]:%s", user_id, p + 1);

                write(fd, msg, strlen(msg));write(cfd, "✅ 发送成功", strlen("✅ 发送成功"));

            }

        }

    }

  

    // 退出：保存数据

    User aus[MAX_USER];

    int ac = load_users(aus, MAX_USER);

    for (int i = 0;i < ac;i++) {

        if (strcmp(aus[i].id, user_id) == 0) {

            pthread_mutex_lock(&mutex);

            for (int j = 0;j < user_count;j++) { if (user_list[j].fd == cfd) { aus[i] = user_list[j];break; } }

            pthread_mutex_unlock(&mutex);

            break;

        }

    }

    save_users(aus, ac);

  

    // 从在线列表删除

    pthread_mutex_lock(&mutex);

    for (int i = 0;i < user_count;i++) { if (user_list[i].fd == cfd) { for (int j = i;j < user_count - 1;j++)user_list[j] = user_list[j + 1];user_count--;break; } }

    pthread_mutex_unlock(&mutex);

  

    close(cfd);

    return NULL;

}

  

void sys_err(const char* str) {

    perror(str);

    exit(1);

}