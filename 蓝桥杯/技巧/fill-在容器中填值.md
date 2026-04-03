---
data: 2026-04-03
---
[C++ 中 fill() 的使用_c++ fill-CSDN博客](https://blog.csdn.net/liu16659/article/details/87152348)


- 头文件 `#include<algorithm>`
`fill()` 是 C++ 标准库算法，**作用是给「左闭右开区间 `[first, last)`」内的所有元素赋值为指定值**，原型：



![[Pasted image 20260403095603.png]]

![[Pasted image 20260403095612.png]]



![[Pasted image 20260403095633.png]]


![[Pasted image 20260403095644.png]]


### 先搞懂 `fill()` 的核心原理

`fill()` 是 C++ 标准库算法，**作用是给「左闭右开区间 `[first, last)`」内的所有元素赋值为指定值**，原型：

cpp

运行

```
template<class ForwardIt, class T>
void fill(ForwardIt first, ForwardIt last, const T& value);
```

- `first`：区间起点（包含）
- `last`：区间终点（不包含，即「最后一个元素的下一个位置」）
- `value`：要填充的值
- **必须包含 `<algorithm>` 头文件**（部分编译器 `<iostream>` 会隐式包含，但标准写法必须显式包含）

---

## 一、你给的例子：静态二维数组的填充

你截图里的代码是**C 风格静态二维数组**（`int G[6][4]`），它的内存是**按行连续存储**的，所以可以用两种方式填充：

### ✅ 方法 1：一次性填充整个数组（截图用的方法）

cpp

运行

```
int G[6][4]; // 6行4列，总元素数 = 6*4 = 24
fill(G[0], G[0] + 6*4, 520);
```

#### 原理拆解：

- `G[0]`：二维数组**第一行第一个元素的地址**，也就是整个数组的**首地址**
- `G[0] + 6*4`：整个数组**最后一个元素（G [5][3]）的下一个位置**（左闭右开的终点）
- `fill` 会把 `[G[0], G[0]+24)` 区间内的所有 24 个元素，全部赋值为 `520`，实现「整个二维数组一键填充」

---

### ✅ 方法 2：逐行填充（更灵活，适合部分填充）

如果不需要填充整个数组，或者不想算总元素数，可以逐行处理：

cpp

运行

```
int G[6][4];
// 逐行填充，每行单独调用fill
for(int i = 0; i < 6; i++){
    fill(G[i], G[i] + 4, 520); // G[i]是第i行的首地址，G[i]+4是第i行末尾的下一个位置
}
```

- 优点：可以只填充前 N 行、跳行填充，灵活度更高
- 效果：和一次性填充完全一致

---

## 二、动态二维数组（`vector<vector<T>>`）的填充（你之前问的双 vector）

**⚠️ 绝对不能用「一次性填充」！**

`vector<vector<T>>` 是「vector 套 vector」，每个内层 vector 的内存是**独立分配**的，不是连续的！直接用 `fill(arr[0].begin(), arr[0].begin()+M*N, val)` 会越界崩溃！

### ✅ 正确写法：逐行填充（必须加引用 `&`）

cpp

运行

```
#include <iostream>
#include <vector>
#include <algorithm> // 必须包含
using namespace std;

int main() {
    const int M = 6, N = 4;
    vector<vector<int>> arr(M, vector<int>(N, 0)); // 初始化6行4列的动态二维数组

    // 方法1：范围for+引用（推荐，简洁）
    for(auto& row : arr){ // 必须加&！否则是拷贝，fill的是临时变量，原数组不会变
        fill(row.begin(), row.end(), 520);
    }

    // 方法2：索引遍历（适合需要控制行的场景）
    // for(int i = 0; i < M; i++){
    //     fill(arr[i].begin(), arr[i].end(), 520);
    // }

    // 打印验证
    for(auto& row : arr){
        for(int x : row) cout << x << " ";
        cout << endl;
    }
    return 0;
}
```

#### 关键注意：

- 必须加 `&`：`auto& row` 是「引用原数组的行」，如果不加 `&`，`row` 是行的拷贝，`fill` 只会修改拷贝，原数组完全不变！
- 必须逐行：因为内层 vector 内存不连续，无法一次性填充整个二维数组

---

## 三、你图论代码常用的「vector 数组」的填充

比如你之前的邻接表 `vector<pair<int, ll>> adj[MAXN];`，这种结构是「**静态数组 + 动态 vector**」，分两种场景：

### 场景 1：重置邻接表（把每个 vector 清空）

cpp

运行

```
const int MAXN = 1005;
vector<pair<int, ll>> adj[MAXN];

// 一键把所有vector清空（fill填充数组本身）
fill(adj, adj + MAXN, vector<pair<int, ll>>());
```

- 原理：`adj` 是数组首地址，`adj+MAXN` 是数组末尾，`val` 是空的`pair<int, ll>` vector，所以每个`adj[i]`都会被赋值为空 vector

### 场景 2：给 vector 内部的元素填充值

如果每个 vector 已经有元素，要统一赋值：

cpp

运行

```
for(int i = 0; i < MAXN; i++){
    fill(adj[i].begin(), adj[i].end(), make_pair(0, 0LL)); // 给每个pair赋值为(0, 0)
}
```

---

## 四、不同二维数组的填充方法对比表

表格

|二维数组类型|推荐填充方式|核心注意事项|
|---|---|---|
|静态 C 风格数组（`int a[M][N]`）|一次性填充：`fill(a[0], a[0]+M*N, val)`|内存连续，可一键填充；必须`<algorithm>`|
|静态 C 风格数组（`int a[M][N]`）|逐行填充：`for(int i=0;i<M;i++) fill(a[i],a[i]+N,val)`|灵活，可填部分行|
|动态`vector<vector<T>>`|逐行填充：`for(auto& row:arr) fill(row.begin(),row.end(),val)`|必须加`&`，绝对不能一次性填充|
|`vector<T> a[MAXN]`（vector 数组）|填充数组：`fill(a,a+MAXN, T())`|给每个 vector 赋值；内部元素需逐 vector 填充|

---

## 五、常见坑点避坑指南

1. **头文件缺失**：`fill` 属于 `<algorithm>`，必须显式包含，否则编译报错！
2. **左闭右开错误**：第二个参数是「最后一个元素的下一个位置」，不能写 `G[0]+6*4-1`，否则会少填最后一个元素！
3. **`vector<vector>` 一次性填充**：内存不连续，直接越界崩溃，必须逐行！
4. **引用遗漏**：`auto row` 是拷贝，`auto& row` 才是原引用，漏加`&`会导致填充无效！
5. **类型不匹配**：`fill` 的`val`类型必须和数组元素类型一致，比如`ll`数组要写`1e9LL`，不能用`int`的`1e9`，避免溢出！

---

### 🎯 结合你图论代码的实战示例

比如 Johnson 算法里的`h[MAXN]`（一维数组）、`dist[MAXN]`（一维数组），用`fill`填充：

cpp

运行

```
const int MAXN = 1005;
const ll INF_DIST = 1e18;
ll h[MAXN], dist[MAXN];

// 一键填充h数组为0
fill(h, h + MAXN, 0LL);
// 一键填充dist数组为无穷大
fill(dist, dist + MAXN, INF_DIST);
```
