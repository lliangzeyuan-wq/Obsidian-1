---
data: 2026-03-01
---
**绝对不是！**

`to_string()` **不止能转 int**，它支持 **所有常见数字类型**！

我给你列清楚，**蓝桥杯能用的全部类型**👇

# 🔥 `to_string` 支持的类型

cpp

运行

```
to_string(int);           // 整数
to_string(long long);      // 长整型（比赛最常用！）
to_string(unsigned int);
to_string(unsigned long long);
to_string(float);          // 浮点数
to_string(double);         // 双精度浮点数
```

也就是说：

- **int** ✅
- **long long** ✅
- **double** ✅
- **float** ✅

**全都能转！**

---

# 👉 比赛里最常用的是：

## 1. 转 int

cpp

运行

```
int x = 123;
string s = to_string(x);
```

## 2. 转 long long（非常重要！）

cpp

运行

```
long long x = 123456789123LL;
string s = to_string(x);
```

---

# 👉 唯一不能转的

**char 类型不能直接用 to_string ()**

但 char 本质是数字，也能强转：

cpp

运行

```
char c = '5';
string s = to_string( (int)c ); // 可以
```

---

# 总结一句话

**to_string 是万能数字转字符串函数，

int、long long、double 全都能转！**

你要我顺便教你 **字符串转回数字** 吗？

`stoi()` `stoll()` `stof()` 这些比赛也超级常用！