[基础篇-14.Redis命令-SortedSet类型_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=15)

# Redis SortedSet 类型常见命令笔记

---

## SortedSet 类型的常见命令

SortedSet 的常见命令有：

- `ZADD key score member`：添加一个或多个元素到 sorted set，如果已经存在则更新其 score 值
- `ZREM key member`：删除 sorted set 中的一个指定元素
- `ZSCORE key member`：获取 sorted set 中的指定元素的 score 值
- `ZRANK key member`：获取 sorted set 中的指定元素的排名
- `ZCARD key`：获取 sorted set 中的元素个数
- `ZCOUNT key min max`：统计 score 值在给定范围内的所有元素的个数
- `ZINCRBY key increment member`：让 sorted set 中的指定元素自增，步长为指定的 increment 值
- `ZRANGE key min max`：按照 score 排序后，获取指定排名范围内的元素
- `ZRANGEBYSCORE key min max`：按照 score 排序后，获取指定 score 范围内的元素
- `ZDIFF`、`ZINTER`、`ZUNION`：求差集、交集、并集
==注意：== 所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可（比如：Z