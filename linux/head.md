用 **`head`** 命令，专门看文件开头若干行。

### ✅ 常用（直接复制用）

bash

运行

```
# 默认前10行
head server.c

# 前5行
head -n 5 server.c

# 前21行
head -n 21 server.c
```

### 💡 顺便：想看中间一段（比如 10–20 行）

bash

运行

```
sed -n '10,20p' server.c
```

要不要我把 `cat`、`head`、`tail`、`less` 这几个最常用的查看文件命令整理成一份速查表给你？