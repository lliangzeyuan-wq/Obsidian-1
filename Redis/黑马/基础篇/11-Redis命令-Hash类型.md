[基础篇-11.Redis命令-Hash类型_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=12)


![[Pasted image 20260522154442.png]]



# Redis Hash 类型常见命令笔记

表格

| 命令        | 作用说明                                                     |
| --------- | -------------------------------------------------------- |
| `HSET`    | `HSET key field value`：添加或修改 Hash 类型 key 的 field 的值      |
| `HGET`    | `HGET key field`：获取 Hash 类型 key 的 field 的值               |
| `HMSET`   | 批量添加多个 Hash 类型 key 的 field 值（注：Redis 4.0+ 推荐用 `HSET` 替代） |
| `HMGET`   | 批量获取多个 Hash 类型 key 的 field 值                             |
| `HGETALL` | 获取 Hash 类型 key 中所有的 field 和 value                        |
| `HKEYS`   | 获取 Hash 类型 key 中所有的 field（字段名）                           |
| `HVALS`   | 获取 Hash 类型 key 中所有的 value（字段值）                           |
| `HINCRBY` | 让 Hash 类型 key 的字段值自增并指定步长（仅支持整数）                         |
| `HSETNX`  | 添加 Hash 类型 key 的 field 值，前提是该 field 不存在，否则不执行            |
### HSET
HSET heima:user:3 age 18
### HGET
HGET heima:user:3 age
### HMSET
HMSET heima:user:3 name Lucy age 18 sex man
### HGETALL
HMSET heima:user:3 name Lucy age 18 sex man

HGETALL heima:user:3 

返回：
```
1)"name"
2)"Lucy"
3)"age"
4)"17"
5)"sex"
6)"woman"
```
### HKEYS
HKEYS heima:user:4

返回：
```
1)"name"
2)"age"
3)"sex"
```
### HVALS
HVALS heima:user:4

返回：
```
1)"LiLei"
2)"20"
3)"man"
```
### HINCRBY
HINCRBY heima:user:4 age -2
### HSETNX
HSET heima:user:3 age 18
- 如果已经存在 ， 返回0 ， 设置失败
- 如果不存在 ， 返回1 ， 设置成功