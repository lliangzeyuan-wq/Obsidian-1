[基础篇-13.Redis命令-Set类型_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=14)

## Set 类型的常见命令

- `SADD key member ...`：向 set 中添加一个或多个元素
- `SREM key member ...`：移除 set 中的指定元素 （rem -> remove
- `SCARD key`：返回 set 中元素的个数
- `SISMEMBER key member`：判断一个元素是否存在于 set 中
- `SMEMBERS key`：获取 set 中的所有元素

### SADD
- xiang
SADD s1 a b c
