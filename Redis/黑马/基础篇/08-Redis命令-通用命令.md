[基础篇-08.Redis命令-通用命令_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=9)

通用指令是**不区分数据类型**、对所有 key 都生效的指令，常见的有：

# 常用命令
FLUSHDB 
- 作用：清空当前数据库 （比如db0等）
FLUSHALL
- 作用：清空所有数据库 （db0~db15)

## 通用命令

表格

当数据量很大的时候  KEYS 如果用模糊查询（模糊查询效率不高），花费的时间会很长，同时又因为Redis是一个单线程，会阻塞服务器

| 命令                   | 功能说明               | 注意事项                                                                    |
| :------------------- | :----------------- | :---------------------------------------------------------------------- |
| `KEYS pattern`       | 查看符合模板的所有 key      | ❌ **不建议在生产环境使用**，会阻塞 Redis                                              |
| `DEL key`            | 删除一个指定的 key        | -                                                                       |
| `EXISTS key`         | 判断 key 是否存在        | -                                                                       |
| `EXPIRE key seconds` | 给 key 设置有效期，到期自动删除 | 时间单位为秒                                                                  |
| `TTL key`            | 查看 key 的剩余有效期      | - 返回正整数：剩余秒数<br><br>- 返回 `-1`：key 存在但无过期时间<br><br>- 返回 `-2`：key 不存在或已过期 |

---

## 查看命令帮助

通过 `help [command]` 可以查看任意命令的具体用法。

示例：查看 `KEYS` 命令的帮助

bash

运行

```
127.0.0.1:6379> help keys
```

输出：

text

```
KEYS pattern
summary: Find all keys matching the given pattern
since: 1.0.0
group: generic
```

- `pattern`：匹配模式（如 `*` 表示匹配所有 key）
- `since: 1.0.0`：该命令从 Redis 1.0.0 版本开始支持
- `group: generic`：属于通用（generic）类命令