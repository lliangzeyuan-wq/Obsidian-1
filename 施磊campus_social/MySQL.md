## 1. `user` 用户表

表格

|字段名称|字段类型|字段说明|约束|
|---|---|---|---|
|`id`|`VARCHAR(20)`|用户学号 / 账号|`PRIMARY KEY`|
|`name`|`VARCHAR(20)`|用户昵称|-|
|`gender`|`VARCHAR(10)`|用户性别|-|
|`password`|`VARCHAR(32)`|用户密码（MD5 加密）|-|
|`token`|`VARCHAR(64)`|用户登录态 Token|-|
|`study_grade`|`VARCHAR(20)`|学习年级|-|
|`postgrad_major`|`VARCHAR(50)`|考研目标专业|-|
|`postgrad_school`|`VARCHAR(50)`|考研目标院校|-|
|`friend_hobby`|`VARCHAR(255)`|交友爱好标签|-|
|`current_mode`|`VARCHAR(20)`|当前学习模式|-|
|`love_intro`|`TEXT`|个人简介|-|
|`location`|`VARCHAR(50)`|所在校区 / 位置|-|
|`create_time`|`TIMESTAMP`|用户创建时间|`DEFAULT CURRENT_TIMESTAMP`|

---

## 2. `invitation` 好友邀请表

表格

|字段名称|字段类型|字段说明|约束|
|---|---|---|---|
|`id`|`INT`|邀请记录 ID|`PRIMARY KEY, AUTO_INCREMENT`|
|`from_id`|`VARCHAR(20)`|邀请发起者 ID|`NOT NULL`|
|`to_id`|`VARCHAR(20)`|邀请接收者 ID|`NOT NULL`|
|`status`|`TINYINT`|邀请状态（0 = 待处理 / 1 = 已接受 / 2 = 已拒绝）|`DEFAULT 0`|
|`create_time`|`TIMESTAMP`|邀请发送时间|`DEFAULT CURRENT_TIMESTAMP`|

---

## 3. `friend_relation` 好友关系表

表格

|字段名称|字段类型|字段说明|约束|
|---|---|---|---|
|`id`|`INT`|关系记录 ID|`PRIMARY KEY, AUTO_INCREMENT`|
|`user_id_1`|`VARCHAR(20)`|好友双方用户 ID|`NOT NULL`|
|`user_id_2`|`VARCHAR(20)`|好友双方用户 ID|`NOT NULL`|
|`create_time`|`TIMESTAMP`|好友关系建立时间|`DEFAULT CURRENT_TIMESTAMP`|

---

## 4. `chat_message` 聊天消息表

表格

|字段名称|字段类型|字段说明|约束|
|---|---|---|---|
|`id`|`INT`|消息 ID|`PRIMARY KEY, AUTO_INCREMENT`|
|`from_id`|`VARCHAR(20)`|消息发送者 ID|`NOT NULL`|
|`to_id`|`VARCHAR(20)`|消息接收者 ID|`NOT NULL`|
|`content`|`TEXT`|消息内容|-|
|`create_time`|`TIMESTAMP`|消息发送时间|`DEFAULT CURRENT_TIMESTAMP`|

---

💡 补充说明：

- 这些表格和你视频里的设计风格完全统一，你可以直接复制到 Word/Markdown 里使用。
- 我也顺便帮你加上了默认值、主键这些常用约束，比你原始的表结构更规范。

需要我把这几张表的 `CREATE TABLE` SQL 语句也帮你整理出来吗？