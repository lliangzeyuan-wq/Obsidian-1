#include <iostream>

#include <cstdio>

#include <cstdlib>

#include <cstring>

#include <unistd.h>

#include <sys/socket.h>

#include <netinet/in.h>

#include <arpa/inet.h>

#include <sys/select.h>

  

#define SERV_PORT 9527

  

void sys_err(const char* str) {

    perror(str);

    exit(1);

}

  

int main() {

    int cfd;

    struct sockaddr_in serv_addr;

    serv_addr.sin_family = AF_INET;

    serv_addr.sin_port = htons(SERV_PORT);

    inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr);

  

    char buf[1024];

    int n;

  

    // 创建客户端socket

    cfd = socket(AF_INET, SOCK_STREAM, 0);

    if (cfd == -1) sys_err("socket创建失败");

  

    // 连接服务器

    if (connect(cfd, (struct sockaddr*)&serv_addr, sizeof(serv_addr)) == -1)

        sys_err("连接服务器失败");

  

    // -------- 学号登录 --------

    std::cout << "请输入你的学号：";

    std::cin >> buf;

    write(cfd, buf, strlen(buf));

    std::cout << "✅ 学号 " << buf << " 登录成功！" << std::endl;

  

    // -------- 选择模式【修复：输错不崩溃，循环重试】--------

    int mode_choice;

    while (true) {

        std::cout << "\n========== 校园多元社交匹配系统 ==========\n";

        std::cout << "1. 学习搭子\n";

        std::cout << "2. 考研搭子\n";

        std::cout << "3. 兴趣交友\n";

        std::cout << "4. 异性恋爱\n";

        std::cout << "=========================================\n";

        std::cout << "请输入选项（1/2/3/4）：";

  

        // 处理输入非数字的情况（输字母/符号不崩溃）

        if (!(std::cin >> mode_choice)) {

            std::cin.clear();

            while (std::cin.get() != '\n');

            std::cout << "❌ 错误！请输入数字 1/2/3/4！" << std::endl;

            continue;

        }

  

        // 发送模式到服务端

        snprintf(buf, sizeof(buf), "MODE:%d", mode_choice);

        write(cfd, buf, strlen(buf));

        memset(buf, 0, sizeof(buf));

        read(cfd, buf, sizeof(buf));

        std::cout << "📌 " << buf << std::endl;

  

        // 模式选择正确则退出循环

        if (strstr(buf, "模式选择错误") == NULL) {

            break;

        }

        std::cout << "❌ 请输入有效数字（1-4）！" << std::endl;

    }

  

    // -------- 填写对应模式资料 --------

    if (mode_choice == 1) {

        char subject[50], grade[20];

        std::cout << "\n请填写学习搭子信息：\n";

        std::cout << "学习科目："; std::cin >> subject;

        std::cout << "年级/专业："; std::cin >> grade;

        snprintf(buf, sizeof(buf), "STUDY:%s|%s", subject, grade);

        write(cfd, buf, strlen(buf));

    }

    else if (mode_choice == 2) {

        char major[50], school[50];

        std::cout << "\n请填写考研搭子信息：\n";

        std::cout << "考研专业："; std::cin >> major;

        std::cout << "目标院校："; std::cin >> school;

        snprintf(buf, sizeof(buf), "POSTGRAD:%s|%s", major, school);

        write(cfd, buf, strlen(buf));

    }

    else if (mode_choice == 3) {

        char hobby[50], personality[20];

        std::cout << "\n请填写兴趣交友信息：\n";

        std::cout << "兴趣爱好："; std::cin >> hobby;

        std::cout << "性格特点："; std::cin >> personality;

        snprintf(buf, sizeof(buf), "FRIEND:%s|%s", hobby, personality);

        write(cfd, buf, strlen(buf));

    }

    else if (mode_choice == 4) {

        char gender[10], intro[100];

        std::cout << "\n请填写异性恋爱信息：\n";

        std::cout << "性别："; std::cin >> gender;

        std::cout << "个人简介："; std::cin >> intro;

        snprintf(buf, sizeof(buf), "LOVE:%s|%s", gender, intro);

        write(cfd, buf, strlen(buf));

    }

  

    // 接收资料保存提示

    memset(buf, 0, sizeof(buf));

    read(cfd, buf, sizeof(buf));

    std::cout << "📝 " << buf << "\n" << std::endl;

  

    // -------- 功能说明 --------

    std::cout << "=====================================================\n";

    std::cout << "               🔥 功能使用说明 🔥                \n";

    std::cout << " 1. 智能匹配：输入 MATCH                          \n";

    std::cout << " 2. 发送申请：@对方学号:REQUEST:申请内容          \n";

    std::cout << " 3. 同意申请：AGREE:对方学号                       \n";

    std::cout << " 4. 拒绝申请：REFUSE:对方学号                      \n";

    std::cout << " 5. 好友聊天：@对方学号:消息内容                   \n";

    std::cout << " 6. 退出客户端：quit                              \n";

    std::cout << "=====================================================\n\n";

  

    // -------- select 无阻塞实时消息（永不卡顿）--------

    fd_set fds;

    int max_fd = (cfd > STDIN_FILENO) ? cfd : STDIN_FILENO;

  

    while (1) {

        FD_ZERO(&fds);

        FD_SET(STDIN_FILENO, &fds);

        FD_SET(cfd, &fds);

  

        int ret = select(max_fd + 1, &fds, NULL, NULL, NULL);

        if (ret < 0) break;

  

        // 实时接收服务器消息

        if (FD_ISSET(cfd, &fds)) {

            memset(buf, 0, sizeof(buf));

            n = read(cfd, buf, sizeof(buf));

            if (n <= 0) {

                std::cout << "\n❌ 与服务器断开连接！" << std::endl;

                break;

            }

            std::cout << "\n📢 系统通知：" << buf << std::endl;

            std::cout << "请输入指令：";

            std::cout.flush();

        }

  

        // 用户输入指令

        if (FD_ISSET(STDIN_FILENO, &fds)) {

            memset(buf, 0, sizeof(buf));

            std::cout << "请输入指令：";

            std::cin >> buf;

  

            write(cfd, buf, strlen(buf));

            if (strcmp(buf, "quit") == 0) break;

        }

    }

  

    close(cfd);

    return 0;

}