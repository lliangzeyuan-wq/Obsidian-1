`find .   -name "http_for_bench"   -type f     -executable`

|部分|含义|大白话解释|
|:--|:--|:--|
|`find`|Linux 里的「文件查找命令」|帮你在电脑里找文件|
|`.`|查找的起始路径|从「当前目录」开始找（你现在在 `build/Debug` 目录，就从这里往下找）|
|`-name "http_for_bench"`|按文件名搜索|只找名字叫 `http_for_bench` 的文件|
|`-type f`|指定文件类型|只找「普通文件」，排除文件夹、符号链接|
|`-executable`|筛选可执行文件|只找带「可执行权限」的文件（就是你编译出来的服务器程序）|


下面给你 **8 个最常用、最实用的 find 例子**，每个都讲得特别直白，你直接复制就能用。

---

## 0. find 基本格式（先记这个）

bash

运行

```
find 去哪里找 什么条件
```

- 去哪里找：`.` 当前目录；`/` 全盘；`/home` 指定目录
- 什么条件：名字、类型、大小、时间、权限等

---

## 1. 按名字找（最常用）

bash

运行

```
find . -name "*.txt"
```

- `.`：从当前目录往下找
- `-name "*.txt"`：文件名以 `.txt` 结尾（区分大小写）

bash

运行

```
find . -iname "readme"
```

- `-iname`：**不区分大小写**（ReadMe、README 都能找到）

---

## 2. 只找文件 / 只找文件夹

bash

运行

```
find . -type f
```

- `-type f`：只找**普通文件**（不显示文件夹）

bash

运行

```
find . -type d
```

- `-type d`：只找**文件夹**

---

## 3. 找可执行文件（你刚才的场景）

bash

运行

```
find . -type f -executable
```

- `-executable`：只找**能直接运行的程序**（绿色那种）

结合名字：

bash

运行

```
find . -name "http_for_bench" -type f -executable
```

---

## 4. 按大小找（查大文件）

bash

运行

```
find . -size +100M
```

- `+100M`：大于 100MB
- 单位：`k` KB、`M` MB、`G` GB

bash

运行

```
find . -size -10k
```

- `-10k`：小于 10KB

---

## 5. 按修改时间找（查最近改了什么）

bash

运行

```
find . -mtime -7
```

- `-mtime -7`：最近 **7 天内**修改过的

bash

运行

```
find . -mtime +30
```

- `+30`：**超过 30 天**没改过的（可清理旧日志）

---

## 6. 按权限找

bash

运行

```
find . -perm 755
```

- `-perm 755`：权限正好是 `rwxr-xr-x` 的文件

bash

运行

```
find . -perm -u+x
```

- `-u+x`：**用户有执行权限**的

---

## 7. 找到后直接执行命令（超实用）

### 例子：找到所有 `.log` 并删除

bash

运行

```
find . -name "*.log" -delete
```

（小心！直接删除，不会问你）

### 例子：给所有 `.sh` 加上执行权限

bash

运行

```
find . -name "*.sh" -exec chmod +x {} \;
```

- `{}`：代表找到的每个文件
- `\;`：结束符

---

## 8. 组合条件（同时满足多个）

bash

运行

```
find . -name "*.cpp" -type f -mtime -3
```

- 意思：当前目录下，**3 天内改过**的 **.cpp 源文件**

---

### 你可以先练这 3 条（最常用）

bash

运行

```
# 1. 找所有 .h 文件
find . -name "*.h"

# 2. 找可执行程序
find . -type f -executable

# 3. 找最近 7 天改的代码
find . -name "*.cpp" -mtime -7
```

要不要我给你出 5 道小练习题（比如 “找出当前目录下大于 50M 的日志文件并删除”），你做我批改？