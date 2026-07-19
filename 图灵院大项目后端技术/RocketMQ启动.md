



- 已经设置了==开启自启==  ，执行  docker ps  看到 `rocketmq-namesrv`、`rocketmq-broker`、`rocketmq-dashboard` 三个容器状态均为 `Up`，即为启动成功。




#### 二、手动启动步骤（自动启动失效时使用）



- root用户


如果开机后容器没自动起来，按以下顺序执行：

1. **启动 Docker 服务**（一般默认自启，没起来时执行）

bash

运行

```
systemctl start docker
```

2. **一键启动全部三个容器**

bash

运行

```
docker start rocketmq-namesrv rocketmq-broker rocketmq-dashboard
```

3. **等待约 30 秒后验证状态**

bash

运行

```
# 查看容器运行状态
docker ps

# 验证核心端口监听
ss -tlnp | grep -E "9876|10911|8082"
```

---

#### 三、启动成功后的访问信息

- **Dashboard 管理页面**：浏览器打开 `http://192.168.198.132:8082`
    
    - 默认账号：`admin`
    - 默认密码：`admin`
    
- **C++ 客户端连接地址**：`192.168.198.132:9876`

---

#### 补充：常用运维命令

- 停止全部服务：`docker stop rocketmq-namesrv rocketmq-broker rocketmq-dashboard`
- 重启全部服务：`docker restart rocketmq-namesrv rocketmq-broker rocketmq-dashboard`
- 实时查看 Broker 日志：`docker logs -f rocketmq-broker`


# 三、conf

rocket-mq: name-server: 192.168.198.132:9876