[基础篇-05.初识Redis-Redis命令行客户端_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=6)

Redis 安装后自带命令行客户端 `redis-cli`，完整用法如下：

---

## 基础语法

bash

运行

```
redis-cli [options] [commands]
```

---

## 常用选项（`options`）

表格

|选项|示例|说明|
|:--|:--|:--|
|`-h`|`-h 127.0.0.1`|指定要连接的 Redis 节点 IP 地址，默认是 `127.0.0.1`|
|`-p`|`-p 6379`|指定要连接的 Redis 节点端口，默认是 `6379`|
|`-a`|`-a 123321`|指定 Redis 的访问密码|

---

## 操作命令（`commands`）

可以直接在 `redis-cli` 后追加命令，也可以进入交互控制台后执行。

表格

|命令|说明|示例|
|:--|:--|:--|
|`ping`|与 Redis 服务端做心跳测试，服务正常会返回 `pong`|`redis-cli ping`|

---

## 两种使用方式

### 方式 1：直接执行命令（不进入交互模式）

bash

运行

```
# 连接本地 Redis 并执行 ping
redis-cli -h 127.0.0.1 -p 6379 -a Ll19299637611@ ping
```

### 方式 2：进入交互控制台

不指定 `commands` 时，会进入 `redis-cli` 的交互模式：

bash

运行

```
redis-cli -h 127.0.0.1 -p 6379 -a Ll19299637611@
```

进入后可直接执行 Redis 命令：

redis

```
127.0.0.1:6379> ping
PONG
```