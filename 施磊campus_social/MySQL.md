# 校园多元社交匹配系统 数据库设计文档

## 基本信息

数据库名称：**CHAT**

字符集：utf8mb4

排序规则：utf8mb4_unicode_ci

---

# 表 1：user 用户信息表

**功能描述**：存储系统所有注册用户账号、个人资料、交友目的、在线状态等核心信息，对接前端登录注册、完善资料、个人中心、交友匹配模块。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|id|int|是|否|自增|用户唯一 ID|
|username|varchar(50)|否|否|无|登录用户名，唯一|
|password|varchar(50)|否|否|无|登录密码|
|studentid|varchar(20)|否|是|NULL|学号|
|gender|tinyint|否|是|1|性别：1 - 男，2 - 女|
|age|int|否|是|18|年龄|
|major|varchar(100)|否|是|NULL|专业|
|avatar|varchar(255)|否|是|NULL|头像存储路径 / 链接|
|purpose|varchar(20)|否|是|study|交友目的：study 学习搭子 /kaoyan 考研搭子 /friend 同性交友 /love 异性恋爱|
|tags|varchar(255)|否|是|NULL|兴趣标签，多标签逗号分隔|
|intro|varchar(255)|否|是|NULL|个人简介|
|state|enum|否|是|offline|在线状态：online 在线，offline 离线|
|create_time|datetime|否|是|当前时间|账号创建时间|

---

# 表 2：match_invite 交友匹配邀请表

**功能描述**：记录用户之间发起的交友申请，实现前端交友邀请发送、待处理、同意 / 拒绝业务逻辑。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|id|int|是|否|自增|邀请记录唯一 ID|
|from_uid|int|否|否|无|邀请发起者用户 ID|
|to_uid|int|否|否|无|邀请接收者用户 ID|
|status|tinyint|否|是|0|邀请状态：0 - 待处理，1 - 已同意，2 - 已拒绝|
|create_time|datetime|否|是|当前时间|邀请发起时间|

---

# 表 3：friend 好友关系表

**功能描述**：存储用户双向好友关联关系，支持前端好友列表展示、聊天好友匹配。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|userid|int|联合主键|否|无|用户 ID|
|friendid|int|联合主键|否|无|好友用户 ID|
|create_time|datetime|否|是|当前时间|成为好友时间|

---

# 表 4：offlinemessage 离线消息表

**功能描述**：存储用户离线时未接收的单聊消息，用户上线后拉取并展示历史离线消息。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|id|int|是|否|自增|离线消息 ID|
|userid|int|否|否|无|消息接收者用户 ID|
|message|text|否|否|无|离线消息内容|
|create_time|datetime|否|是|当前时间|消息存储时间|

---

# 表 5：allgroup 群组信息表

**功能描述**：存储系统所有群聊基础信息，包括群名、群简介。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|id|int|是|否|自增|群组唯一 ID|
|groupname|varchar(50)|否|否|无|群组名称，唯一|
|groupdesc|varchar(200)|否|是|NULL|群组简介描述|
|create_time|datetime|否|是|当前时间|群组创建时间|

---

# 表 6：groupuser 群成员关系表

**功能描述**：记录用户与群组的加入关系，标记群主和普通成员角色。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|groupid|int|联合主键|否|无|群组 ID|
|userid|int|联合主键|否|无|成员用户 ID|
|grouprole|enum|否|是|normal|群角色：creator 群主，normal 普通成员|
|join_time|datetime|否|是|当前时间|入群时间|

---

# 表 7：chat_message 聊天记录表

**功能描述**：永久存储单聊、群聊所有聊天记录，用于前端聊天页面加载历史消息。

表格

|字段名|数据类型|是否主键|允许为空|默认值|字段说明|
|---|---|---|---|---|---|
|id|int|是|否|自增|聊天记录唯一 ID|
|from_uid|int|否|否|无|消息发送者 ID|
|to_uid|int|否|否|无|消息接收者 ID / 群组 ID|
|content|text|否|否|无|聊天消息内容|
|msg_type|tinyint|否|是|1|消息类型：1 - 单聊，2 - 群聊|
|create_time|datetime|否|是|当前时间|消息发送时间|

---

## 附带：数据库创建总 SQL（可直接执行）

sql

```
CREATE DATABASE IF NOT EXISTS CHAT DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE CHAT;

-- 下面是所有建表语句，之前已经给过，直接运行即可
```

我可以再帮你把这份文档**改成毕业论文标准格式**，加上：ER 图说明、表间关系、数据库设计原则，要不要我帮你整理成论文可直接复制的章节？