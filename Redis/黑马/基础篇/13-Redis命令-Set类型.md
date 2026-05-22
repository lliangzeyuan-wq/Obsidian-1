[基础篇-13.Redis命令-Set类型_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=14)

## Set 类型的常见命令

- `SADD key member ...`：向 set 中添加一个或多个元素
- `SREM key member ...`：移除 set 中的指定元素 （rem -> remove
- `SCARD key`：返回 set 中元素的个数
- `SISMEMBER key member`：判断一个元素是否存在于 set 中
- `SMEMBERS key`：获取 set 中的所有元素
- `SINTER key1 key2 ...`：求 `key1` 与 `key2` 的交集 
- `SDIFF key1 key2 ...`：求 `key1` 与 `key2` 的差集
- `SUNION key1 key2 ...`：求 `key1` 和 `key2` 的并集

### SADD
- 向集合s1里面加入 a b c
SADD s1 a b c
### SREM
- rem -> remove
SREM s1 a
### SCARD
SCARD s1
### SISMEMBER
SISMEMBER s1 a
- 如果a在集合s1里面，返回1
- 如果不在，返回0
### SMEMBERS key
SMEMBERS s1
- 返回
```
1)"a"
2)"b"
3)"c"
```