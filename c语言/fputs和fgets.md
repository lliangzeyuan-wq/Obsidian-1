
## 1. `fputs`：往 “文件 / 流” 里写字符串

### 函数原型

c

运行

```
int fputs(const char *str, FILE *stream);
```

- 功能：把字符串 `str` 写入指定的 `stream`（流）里。
- 特点：**不会自动加换行符**，和 `puts` 不一样。

### 你代码里的用法

c

运行

```
fputs("Input message(Q to quit): ", stdout);
```

- `stdout` 就是 “标准输出流”，也就是终端屏幕。
- 作用：在屏幕上打印提示文字 `Input message(Q to quit):` ，让用户知道现在可以输入消息了。
- 为什么不用 `puts`？因为 `puts` 会自动加换行，会把提示文字单独占一行，而这里我们希望提示和用户输入在同一行，所以用 `fputs`。

---

## 2. `fgets`：从 “文件 / 流” 里读字符串

### 函数原型

c

运行

```
char *fgets(char *str, int n, FILE *stream);
```

- 功能：从指定的 `stream` 中读取一行字符串，存到 `str` 里，最多读 `n-1` 个字符（留 1 个位置给结束符`\0`）。
- 特点：安全、会自动加上 `\0`，而且会把用户输入的**换行符 `\n` 也一起读进来**。

### 你代码里的用法

c

运行

```
fgets(message, BUF_SIZE, stdin);
```

- `stdin` 是 “标准输入流”，也就是键盘。
- 作用：读取用户在键盘上输入的一行文字，存到 `message` 数组里，最多读 `BUF_SIZE-1` 个字符，防止溢出。
- 注意：比如你输入 `hello` 然后按回车，`message` 里存的是 `"hello\n"`，后面的退出判断 `strcmp(message, "q\n")` 就是利用了这个特性。

---

## 3. 结合这段代码看它们的完整流程

c

运行

```
while(1)
{
    // 1. 用 fputs 打印提示，让用户输入
    fputs("Input message(Q to quit): ", stdout);
    // 2. 用 fgets 读取用户输入的一行文字
    fgets(message, BUF_SIZE, stdin);

    // 3. 判断用户是不是输入了 q/Q 回车，如果是就退出循环
    if(!strcmp(message, "q\n") || !strcmp(message, "Q\n"))
        break;
}
```

流程就是：

1. 提示用户输入 → 2. 读取用户输入 → 3. 判断是否退出 → 4. 发送给服务器

---

## 4. 补充：和 `gets`/`puts`/`scanf` 的区别

表格

|函数|输入 / 输出|安全吗？|特点|
|:--|:--|:--|:--|
|`fgets`|输入|✅ 安全|限制最大长度，会读入换行符|
|`gets`|输入|❌ 危险|无长度限制，已被废弃|
|`fputs`|输出|✅ 安全|不自动加换行，适合和输入同一行|
|`puts`|输出|✅ 安全|自动加换行|
|`scanf`|输入|❌ 有坑|遇到空格就停止，无法读入整行|

---

## 一句话总结

- `fputs("xxx", stdout)`：在屏幕上打印文字，不自动换行。
- `fgets(buf, n, stdin)`：从键盘读取一行文字，安全、会带换行符。

要不要我把这段 `while` 循环的后续代码（`write`/`read` 收发消息的部分）也给你拆解开？这样你就能完整理解整个回声客户端的工作流程了。