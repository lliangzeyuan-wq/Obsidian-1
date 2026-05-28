
我来给你讲清楚 `getuid()`/`geteuid()` 和 `getgid()`/`getegid()` 这两组函数，以及它们背后的 “实际 ID” 和 “有效 ID” 到底有什么区别👇

---

## 一、核心概念：进程的两种身份 ID

每个 Linux 进程都有两套身份：

- **实际用户 / 组 ID（RUID/RGID）**：这个进程是谁启动的（比如你用 `lzy` 运行程序，RUID 就是 `lzy` 的 UID）。
- **有效用户 / 组 ID（EUID/EGID）**：进程当前 “正在扮演谁”，决定了它能访问哪些文件 / 资源（比如执行带 `suid` 权限的程序时，EUID 会变成文件所有者的 UID）。

---

## 二、`getuid()` 与 `geteuid()`：用户 ID

c

运行

```
#include <unistd.h>
#include <sys/types.h>

uid_t getuid(void);   // 获取 实际用户ID (RUID)
uid_t geteuid(void);  // 获取 有效用户ID (EUID)
```

### 1. `getuid()`：我 “本来” 是谁

- 作用：返回**启动这个进程的用户的 UID**。
- 特点：除非你用 `setuid()` 显式修改，否则进程的 `getuid()` 从始至终都不会变。
- 例子：你用普通用户 `lzy` 运行程序，`getuid()` 就一直是 `lzy` 的 UID（比如 1000）。

### 2. `geteuid()`：我 “现在” 是谁

- 作用：返回进程当前的**有效用户 ID**，决定了进程的文件访问权限。
- 特点：会随着进程的权限变化而变化，最典型的场景是执行带 `SUID` 权限的程序。
- 例子：
    
    - 普通程序：`geteuid()` 和 `getuid()` 相等，都是 1000。
    - 带 `SUID root` 的程序：`geteuid()` 会变成 0（root），而 `getuid()` 还是 1000。
    

---

## 三、`getgid()` 与 `getegid()`：用户组 ID

和上面完全对应，只是从 “用户” 变成了 “用户组”：

c

运行

```
gid_t getgid(void);   // 获取 实际用户组ID (RGID)
gid_t getegid(void);  // 获取 有效用户组ID (EGID)
```

### 1. `getgid()`：我本来属于哪个组

- 作用：返回**启动进程的用户的默认组 GID**，除非显式修改，否则不变。

### 2. `getegid()`：我现在属于哪个组

- 作用：返回进程当前的**有效组 ID**，决定了进程对文件的组权限。
- 特点：执行带 `SGID` 权限的程序时，`getegid()` 会变成文件所属组的 GID。

---

## 四、最直观的例子：passwd 命令

Linux 系统里的 `passwd` 命令就是典型的 SUID 程序：

- 文件权限：`-rwsr-xr-x 1 root root ... /usr/bin/passwd`
    
    - `s` 表示设置了 SUID 权限，执行时进程的 EUID 会变成文件所有者 `root` 的 UID。
    

当普通用户运行 `passwd` 时：

- `getuid()` → 你的 UID（比如 1000），表示 “我本来是普通用户”。
- `geteuid()` → 0（root），表示 “我现在正在以 root 权限运行”。
- 所以它能修改只有 root 才能写的 `/etc/shadow` 文件。

---

## 五、总结对比表

表格

|函数|获取的 ID|核心作用|是否会变化|
|:--|:--|:--|:--|
|`getuid()`|实际用户 ID (RUID)|谁启动了进程|一般不变|
|`geteuid()`|有效用户 ID (EUID)|进程当前的权限身份|会随 SUID/SGID 变化|
|`getgid()`|实际组 ID (RGID)|启动用户的默认组|一般不变|
|`getegid()`|有效组 ID (EGID)|进程当前的组权限|会随 SGID 变化|

---

要不要我给你写一段可以直接编译运行的示例代码，帮你直观看到普通程序和 SUID 程序下这四个函数的返回值差异？

下面用两个简单、可直接编译运行的例子，说明 `getuid()` 和 `geteuid()` 的区别。

---

## 示例 1：普通程序（无 SUID 权限）

c

运行

```
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main(void)
{
    printf("getuid()  = %u\n", getuid());
    printf("geteuid() = %u\n", geteuid());
    return 0;
}
```

### 编译运行

bash

运行

```
gcc uid_test.c -o uid_test
./uid_test
```

### 输出（普通用户运行）

plaintext

```
getuid()  = 1000
geteuid() = 1000
```

此时两个值**相等**，因为程序没有设置 SUID 权限，进程的 “实际用户 ID” 和 “有效用户 ID” 都是启动它的普通用户 ID。

---

## 示例 2：带 SUID 权限的程序（关键对比）

### 1. 先编译普通程序

bash

运行

```
gcc uid_test.c -o uid_test
```

### 2. 给程序设置 SUID root 权限

bash

运行

```
sudo chown root:root uid_test
sudo chmod u+s uid_test
```

此时程序的权限会变成：`-rwsr-xr-x root root ... uid_test`，其中 `s` 表示 SUID 位已开启。

### 3. 再用普通用户运行

bash

运行

```
./uid_test
```

### 输出

plaintext

```
getuid()  = 1000   # 不变，还是启动进程的用户ID
geteuid() = 0      # 变了，变成文件所有者 root 的ID
```

### 关键结论

- `getuid()`：**永远是启动进程的用户 ID**，不会因为 SUID 权限改变。
- `geteuid()`：**进程当前的有效用户 ID**，会根据文件的 SUID 权限动态变化，决定了进程能访问哪些资源。

---

## 补充说明

- `passwd` 命令就是典型的 SUID 程序，它能修改 `/etc/shadow` 文件，就是因为 `geteuid()` 变成了 0（root），而 `getuid()` 仍然是普通用户的 ID。
- 只有可执行文件设置了 SUID 位时，`geteuid()` 才会和 `getuid()` 不同。

---

需要我再给你补充一段关于 `getgid()` 和 `getegid()` 的对比代码吗？