[基础篇-09.Redis命令-String类型_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=10)

# Redis String 类型常见命令笔记

表格

| 命令            | 作用说明                                             |
| ------------- | ------------------------------------------------ |
| `SET`         | 添加或修改已存在的一个 String 类型键值对                         |
| `GET`         | 根据 key 获取 String 类型的 value                       |
| `MSET`        | 批量添加多个 String 类型的键值对                             |
| `MGET`        | 根据多个 key 获取多个 String 类型的 value                   |
| `INCR`        | 让一个整型的 key 自增 1                                  |
| `INCRBY`      | 让一个整型的 key 自增并指定步长，例如：`incrby num 2` 让 num 值自增 2 |
| `INCRBYFLOAT` | 让一个浮点类型的数字自增并指定步长                                |
| `SETNX`       | 添加一个 String 类型的键值对，前提是这个 key 不存在，否则不执行           |
| `SETEX`       | 添加一个 String 类型的键值对，并且指定有效期                       |
|               |                                                  |

### set
SET name jack
### get
GET name
### MSET
MSET k1 v1 k2 v2 k3 v3
### MGET
MGET name age k1 k2 k3
### INCR 
INCR age
### INCRBY
INCRBY age -3
### INCRBYFLOAT 
SET num 10.1
INCRBYFLOAT num -0.98
### setnx
SETNX name jack
- 只有当name不存在的时候会设置 返回1
- 当name存在，返回0 ， 不修改
### setex
- 相当于set + expire(指定存在的时间)
SETEX name 10 jack
==> SET name jack EX 10