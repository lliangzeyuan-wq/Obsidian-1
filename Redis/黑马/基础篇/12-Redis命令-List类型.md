[基础篇-12.Redis命令-List类型_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=13)

List 类型

Redis 中的 List 类型与 Java 中的 LinkedList 类似，可以看做是一个==双向链表结构==。既可以支持正向检索和也可以支持反向检索。

特征也与 LinkedList 类似：

- 有序
- 元素可以重复
- 插入和删除快
- 查询速度一般

常用来存储一个有序数据，例如：朋友圈点赞列表，评论列表等。

![[Pasted image 20260522171407.png]]

## List 类型的常见命令

List 的常见命令有：

- `LPUSH key element ...`：向列表左侧插入一个或多个元素
- `LPOP key`：移除并返回列表左侧的第一个元素，没有则返回 nil
- `RPUSH key element ...`：向列表右侧插入一个或多个元素
- `RPOP key`：移除并返回列表右侧的第一个元素
- `LRANGE key num1 num2`：返回一段下标范围内的所有元素
- `BLPOP` 和 `BRPOP`：与 `LPOP` 和 `RPOP` 类似，只不过在没有元素时==等待指定时间==(阻塞），而不是直接返回 `nil`

---

### 操作示意图说明

- **左侧操作**：`LPUSH`（从左插入）、`LPOP`（从左弹出）
- **右侧操作**：`RPUSH`（从右插入）、`RPOP`（从右弹出）


### LPUSH
LPUSH users 1 2 3 
- 相当于链表的头插
![[Pasted image 20260522171655.png]]

### LPOL
LPOL users 2 
- 从users左边弹出2个
- 返回：
```
1)"3"
2)"4"
```

### LRANGE 
LRANGE users 2 4
![[Pasted image 20260522172042.png]]

- 返回(注意在程序里面还是从0开始计数的)
```
1)"4"
2)"3"
3)"2"
```


### BLPOP
BLPOP  users2 100
- 100指的是等待100s，这个后面的数字是必须写的，因为如果不写的话计算机不知道要等待多久
