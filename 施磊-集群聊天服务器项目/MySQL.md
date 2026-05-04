

![[Pasted image 20260503093810.png]]

# 校园社交匹配系统 - 数据库设计文档

## 数据库名称：`chat`

### 一、用户表（`user`）

**表说明**：存储系统用户的基本信息，包括登录账号、密码和在线状态。

表格

|字段名称|字段类型|字段说明|约束|
|:--|:--|:--|:--|
|`id`|INT|用户 ID（主键）|PRIMARY KEY、AUTO_INCREMENT、NOT NULL|
|`name`|VARCHAR(50)|用户名 / 账号|UNIQUE、NOT NULL|
|`password`|VARCHAR(50)|用户密码|NOT NULL|
|`state`|ENUM('online', 'offline')|用户在线状态|DEFAULT 'offline'|

---

### 二、好友关系表（`friend`）

**表说明**：记录用户之间的好友关系，是一个多对多关联表。

表格

|字段名称|字段类型|字段说明|约束|
|:--|:--|:--|:--|
|`userid`|INT|用户 ID|PRIMARY KEY（联合主键）、NOT NULL|
|`friendid`|INT|好友 ID|PRIMARY KEY（联合主键）、NOT NULL|

---

### 三、群组表（`allgroup`）

**表说明**：存储所有群组的基本信息。

表格

|字段名称|字段类型|字段说明|约束|
|:--|:--|:--|:--|
|`id`|INT|群组 ID（主键）|PRIMARY KEY、AUTO_INCREMENT、NOT NULL|
|`groupname`|VARCHAR(50)|群组名称|NOT NULL|
|`groupdesc`|VARCHAR(200)|群组功能描述|可为空|

---

### 四、群组成员表（`groupuser`）

**表说明**：记录用户与群组的关联关系，以及用户在群内的角色。

表格

|字段名称|字段类型|字段说明|约束|
|:--|:--|:--|:--|
|`groupid`|INT|群组 ID|PRIMARY KEY（联合主键）、NOT NULL|
|`userid`|INT|用户 ID|PRIMARY KEY（联合主键）、NOT NULL|
|`grouprole`|ENUM('creator', 'normal')|用户在群内的角色（创建者 / 普通成员）|DEFAULT 'normal'|

---

### 五、离线消息表（`offlinemessage`）

**表说明**：存储用户离线期间收到的消息，用户上线后可拉取查看。

表格

|字段名称|字段类型|字段说明|约束|
|:--|:--|:--|:--|
|`userid`|INT|接收消息的用户 ID|NOT NULL|
|`message`|VARCHAR(500)|离线消息内容|NOT NULL|

---

需要我把这些表的**创建 SQL 语句**也帮你整理出来，方便你直接导入数据库吗？