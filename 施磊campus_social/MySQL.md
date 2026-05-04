
# chat_

|字段名称|字段类型|字段说明|约束|
|---|---|---|---|
|id|INT|消息 ID|PRIMARY KEY、AUTO_INCREMENT|
|from_id|VARCHAR(20)|发送者 ID|-|
|to_id|VARCHAR(20)|接收者 ID|-|
|content|TEXT|消息内容|-|
|create_time|TIMESTAMP|发送时间|DEFAULT CURRENT_TIMESTAMP|



# friend_relation

|字段名称|字段类型|字段说明|约束|
|---|---|---|---|
|id|INT|好友关系记录 ID|PRIMARY KEY、AUTO_INCREMENT|
|user_id_1|VARCHAR(20)|好友双方用户 ID|-|
|user_id_2|VARCHAR(20)|好友双方用户 ID|-|
|create_time|TIMESTAMP|好友关系建立时间|DEFAULT CURRENT_TIMESTAMP|
