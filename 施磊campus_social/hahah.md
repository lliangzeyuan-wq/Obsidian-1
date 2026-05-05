我给你**最简、最快、能直接跑起来**的步骤！全程复制命令，10 分钟内运行完整项目（Web 前端 + C++ 后端 + 数据库），适配你的 Ubuntu 虚拟机👇

# 一、先安装环境依赖（复制执行）

打开终端，一次性安装所有必备工具：

bash

运行

```
sudo apt update
sudo apt install -y git g++ cmake make libmysqlclient-dev redis-server nginx libmuduo-dev nodejs npm
```

# 二、克隆项目到本地

bash

运行

```
# 克隆项目
git clone https://github.com/haojiubudaqiu/chatserver.git
# 进入项目
cd chatserver
```

# 三、配置 MySQL 数据库（核心步骤）

### 1. 登录 MySQL（默认无密码，直接回车）

bash

运行

```
mysql -u root -p
```

### 2. 执行这 3 条命令，创建数据库 + 导入表

sql

```
CREATE DATABASE chat;
USE chat;
source ./chat.sql;
exit;
```

### 3. 修改数据库密码（和项目配置一致）

bash

运行

```
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
exit
```

# 四、编译后端 C++ 服务器

bash

运行

```
# 创建编译目录
mkdir build && cd build
# 编译
cmake ..
make
```

✅ 编译成功标志：出现 `[100%] Built target ChatServer`

# 五、启动后端服务

bash

运行

```
# 回到bin目录
cd ../bin
# 启动服务器
./ChatServer
```

✅ 成功标志：终端卡住不动，监听 6000 端口，无报错

# 六、启动 Web 前端（Vue3，展示用）

**新开一个终端**，执行：

bash

运行

```
cd chatserver/webclient
# 安装依赖
npm install
# 启动前端
npm run dev
```

✅ 成功标志：控制台给出访问地址，例如 `http://localhost:5173`

# 七、打开浏览器测试

1. 访问前端地址：`http://localhost:5173`
2. 注册账号 → 登录 → 开始聊天
3. 可多开浏览器窗口，测试**好友聊天、群聊、离线消息**全功能

---

# 🔥 项目展示直接用这个！

- **后端**：C++ Muduo 高并发服务器
- **前端**：Vue3 美观网页
- **功能**：注册 / 登录 / 私聊 / 群聊 / 离线消息
- **技术**：WebSocket + MySQL + Redis

---

## 遇到问题直接告诉我：

1. 端口占用
2. 数据库连接失败
3. 前端启动报错
    
    我直接帮你一键解决！