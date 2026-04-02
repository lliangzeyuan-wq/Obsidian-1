---
data: 2025-12-26
---
### map 基本概念

- 简介:
    - map 中所有元素都是 pair
    - pair 中第一个元素为 key（键值），起到索引作用，第二个元素为 value（实值）
    - 所有元素都会根据元素的键值自动排序
- 本质:
    - map/multimap 属于关联式容器，底层结构是用二叉树实现。
- 优点:
    - 可以根据 key 值快速找到 value 值
- map 和 multimap 区别:
    - map 不允许容器中有重复 key 值元素
    - multimap 允许容器中有重复 key 值元素
