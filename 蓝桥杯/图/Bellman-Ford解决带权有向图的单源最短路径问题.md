---
data: 2026-04-02
---
# Bellman-Ford 算法深度解析（适用场景 + 原理 + C++ 实现）

Bellman-Ford（贝尔曼 - 福特）算法是**单源最短路径算法**，核心优势是**支持负权边**，且能**检测负权环**—— 这是 Dijkstra 算法做不到的。

## 一、适用问题

### 1. 核心适用场景

解决**带权有向图**的**单源最短路径问题**：从指定起点出发，求到所有其他节点的最短路径。

### 2. 必须用它的场景

- **图中存在负权边**：Dijkstra 算法会因负权边导致贪心策略失效（误判最短路径），Bellman-Ford 可精准处理；
- **需要检测负权环**：负权环（环的总权值为负）会导致路径权值无限减小，最短路径不存在，Bellman-Ford 能精准检测；
- **小规模图计算**：时间复杂度较高，适合节点数较少的图（如 n≤1000）。

### 3. 不适用场景

- **大规模稀疏图**：优先选堆优化 Dijkstra（O(mlogn)）；
- **无负权边的大规模图**：Dijkstra 效率远高于 Bellman-Ford；
- **全源最短路径**：选 Floyd 算法（O(n3)），但需注意 Floyd 也不能处理负权环。

## 二、算法原理

### 1. 核心概念

#### （1）松弛操作（Relaxation）

对边 u→v（权值 w），若「起点到 u 的距离 + w」<「起点到 v 的距离」，则更新 v 的最短距离。

公式：`if (dist[v] > dist[u] + w) dist[v] = dist[u] + w;`

大白话：**通过这条边，尝试找到到 v 的更短路径，能更短就更新**。

#### （2）为什么要做 n−1 轮松弛？

n 为节点数，**无环图的最短路径最多包含 n−1 条边**。

- 第 1 轮松弛：找到最多含 1 条边的最短路径；
- 第 2 轮松弛：找到最多含 2 条边的最短路径；
- ...
- 第 n−1 轮松弛：找到最多含 n−1 条边的最短路径。
    
    完成 n−1 轮后，所有节点的最短距离会稳定，不再更新。

#### （3）负权环检测

若完成 n−1 轮松弛后，**仍能对某条边进行松弛**，说明图中存在**从起点可达的负权环**（绕环走一圈权值减小，可无限绕圈导致路径权值趋近负无穷，最短路径无意义）。

### 2. 算法步骤

1. **初始化**：起点距离设为 0，其他节点距离设为无穷大（`INF`）；
2. **n−1 轮松弛**：遍历所有边，重复 n−1 次松弛操作，逐步逼近最短路径；
3. **负权环检测**：再遍历一次所有边，若仍能松弛，说明存在负权环，算法结束。

## 三、C++ 完整实现

### 1. 代码结构

- 用**边结构体**存储图（`from` 起点、`to` 终点、`weight` 权值）；
- 距离数组 `dist` 存储起点到各节点的最短距离；
- 包含**松弛迭代**、**负权环检测**核心逻辑，加详细注释。

### 2. 完整代码
```cpp
#include <iostream>
#include <vector>
#include <climits> // 用于 INT_MAX（无穷大）
using namespace std;

// 边结构体：存储一条有向边的起点、终点、权值
struct Edge {
    int from;   // 边的起点
    int to;     // 边的终点
    int weight; // 边的权值（可正可负）

    // 构造函数
    Edge(int f, int t, int w) : from(f), to(t), weight(w) {}
};

/**
 * Bellman-Ford 算法核心函数
 * @param n 图的节点总数（节点编号从 0 开始）
 * @param edges 图的边集合
 * @param start 起始节点（单源起点）
 * @param dist 输出参数：存储起点到各节点的最短距离
 * @return bool  true=无负权环，计算完成；false=存在负权环，无有效最短路径
 */
bool bellmanFord(int n, const vector<Edge>& edges, int start, vector<long long>& dist) {
    // 1. 初始化距离数组：起点距离为 0，其余节点设为无穷大（用 long long 避免溢出）
    const long long INF = INT_MAX;
    dist.assign(n, INF);
    dist[start] = 0;

    // 2. 进行 n-1 轮松弛操作（核心步骤）
    for (int i = 0; i < n - 1; ++i) {
        bool updated = false; // 优化：标记本轮是否有更新，无更新则提前退出

        // 遍历所有边，尝试松弛
        for (const Edge& e : edges) {
            // 仅当起点到 e.from 可达（dist[e.from] 不是无穷大）时，才松弛 e.to
            if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
                dist[e.to] = dist[e.from] + e.weight;
                updated = true; // 标记有更新
            }
        }

        // 优化：本轮无更新，说明已找到最短路径，提前退出（减少不必要计算）
        if (!updated) {
            break;
        }
    }

    // 3. 检测负权环（额外遍历一次所有边）
    for (const Edge& e : edges) {
        if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
            return false; // 仍能松弛，存在负权环
        }
    }

    return true; // 无负权环，计算完成
}

// 辅助函数：打印结果
void printResult(int n, const vector<long long>& dist, bool hasNegativeCycle) {
    if (hasNegativeCycle) {
        cout << "❌ 图中存在从起点可达的负权环，无有效最短路径！" << endl;
        return;
    }

    cout << "✅ 无负权环，起点到各节点的最短距离如下：" << endl;
    for (int i = 0; i < n; ++i) {
        if (dist[i] == INT_MAX) {
            cout << "节点 " << i << "：不可达" << endl;
        } else {
            cout << "节点 " << i << "：" << dist[i] << endl;
        }
    }
}

// 主函数：测试用例
int main() {
    // 测试用例 1：无负权环的图（正常计算）
    cout << "========== 测试用例 1：无负权环 ==========" << endl;
    int n1 = 5; // 5 个节点（0~4）
    vector<Edge> edges1;
    // 构建边：(起点, 终点, 权值)
    edges1.emplace_back(0, 1, 2);  // 0->1，权值 2
    edges1.emplace_back(0, 2, 5);  // 0->2，权值 5
    edges1.emplace_back(1, 2, 1);  // 1->2，权值 1（负权边，验证算法）
    edges1.emplace_back(1, 3, 4);  // 1->3，权值 4
    edges1.emplace_back(2, 3, 1);  // 2->3，权值 1
    edges1.emplace_back(2, 4, 3);  // 2->4，权值 3
    edges1.emplace_back(3, 4, 2);  // 3->4，权值 2

    vector<long long> dist1;
    bool res1 = bellmanFord(n1, edges1, 0, dist1);
    printResult(n1, dist1, res1);

    // 测试用例 2：含负权环的图（验证负环检测）
    cout << "\n========== 测试用例 2：含负权环 ==========" << endl;
    int n2 = 4; // 4 个节点（0~3）
    vector<Edge> edges2;
    // 构建边：0->1(1)、1->2(-3)、2->1(1)（构成负权环：1->2->1，总权值 -3+1=-2）
    edges2.emplace_back(0, 1, 1);
    edges2.emplace_back(1, 2, -3);
    edges2.emplace_back(2, 1, 1);
    edges2.emplace_back(1, 3, 2);

    vector<long long> dist2;
    bool res2 = bellmanFord(n2, edges2, 0, dist2);
    printResult(n2, dist2, res2);

    return 0;
}
```

### 3. 代码说明

1. **边结构体**：统一存储有向边的三个核心属性，适配 Bellman-Ford 「遍历所有边」的需求；
2. **初始化**：用 `INT_MAX` 表示无穷大，`long long` 避免加法溢出（避免 int 溢出导致错误）；
3. **n−1 轮松弛**：加 `updated` 优化，无更新时提前退出，提升效率；
4. **负环检测**：额外遍历一次边，若能松弛则返回 `false`，精准识别负权环；
5. **测试用例**：覆盖「无负权环（含负权边）」和「含负权环」两种场景，验证算法正确性。

## 四、复杂度分析

- **时间复杂度**：O(n×m)（n 节点数，m 边数）。n−1 轮遍历，每轮遍历 m 条边；
- **空间复杂度**：O(n)（仅需存储距离数组，边集合存储为 O(m)，属于输入空间）。

## 五、与 Dijkstra 算法核心对比

表格

|特性|Dijkstra 算法|Bellman-Ford 算法|
|---|---|---|
|适用图|仅**正权图**（无负权边）|**全权图**（支持负权边）|
|核心功能|单源最短路径|单源最短路径 + **负权环检测**|
|时间复杂度|堆优化：O(mlogn)|O(n×m)|
|效率|高（适合大规模图）|低（适合小规模图）|
|适用场景|无负权边的大规模图|含负权边、需检测负环的小规模图|

## 六、进阶优化

Bellman-Ford 效率较低，实际工程中常用 **SPFA**（Shortest Path Faster Algorithm）—— 队列优化的 Bellman-Ford，平均时间复杂度 O(m)，最坏 O(n×m)，且能高效处理负权边和负环检测。

需要我补充 SPFA 的 C++ 实现代码吗？




## 一、极致详细注释版完整代码

cpp

运行

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

// 边结构体：Bellman-Ford 是「边集驱动」的算法，需要用结构体统一存储所有边的信息
struct Edge {
    int from;   // 边的【起点 u】：这条边从节点 u 出发
    int to;     // 边的【终点 v】：这条边到节点 v 结束
    int weight; // 边的【权值 w】：u→v 这条边的长度（支持正、负、0）

    // 构造函数：方便快速创建边对象，简化代码
    Edge(int f, int t, int w) : from(f), to(t), weight(w) {}
};

/**
 * @brief Bellman-Ford 单源最短路径算法（核心优势：支持负权边、可检测负权环）
 * 
 * @param n 图中【节点总数】（节点编号需连续，通常为 0~n-1 或 1~n）
 * @param edges 图的【边集合】：存储图中所有的有向边
 * @param start 【单源起点】：从哪个节点出发，求到所有其他节点的最短路径
 * @param dist 【输出参数（引用传递）】：算法执行后，dist[i] 存储「起点到节点 i 的最短距离」
 * 
 * @return true  图中【无负权环】，最短路径计算完成，dist 数组有效
 * @return false 图中【存在从起点可达的负权环】，最短路径无意义，dist 数组无效
 * 
 * 🔴 核心特性：
 * 1. 支持【负权边】：Dijkstra 算法无法处理负权边，Bellman-Ford 可以
 * 2. 可【检测负权环】：判断图中是否存在总权值为负的环（会导致最短路径无限小）
 * 3. 时间复杂度 O(n*m)，适合小规模图（n≤1000），大规模图优先用 Dijkstra/SPFA
 */
bool bellmanFord(int n, const vector<Edge>& edges, int start, vector<long long>& dist) {
    // ==============================================
    // 1. 初始化距离数组（算法的准备步骤）
    // ==============================================
    // 🔴 定义「无穷大」：表示节点初始时不可达
    // 注意：必须用 long long 类型！彻底避免 int 加法溢出导致的逻辑错误
    // 用 1e18 替代 INT_MAX：INT_MAX 加正数会溢出变负数，导致松弛条件判断错误
    const long long INF = 1e18;

    // 🔵 初始化距离数组：将所有节点的距离设为「无穷大」（不可达）
    // assign(n, INF) 会把 dist 数组的大小重置为 n，每个元素赋值为 INF
    dist.assign(n, INF);

    // 🟢 起点到自己的距离永远是 0（单源最短路径的起点，所有松弛操作的源头）
    dist[start] = 0;

    // ==============================================
    // 2. 执行 n-1 轮松弛操作（算法核心）
    // ==============================================
    // 🔴 为什么是 n-1 轮？
    // 在一个有 n 个节点的图中，【无环的最短路径】最多包含 n-1 条边（不可能有环，否则不是最短）
    // 所以最多需要 n-1 轮松弛，就能把所有节点的最短路径都更新完成
    for (int i = 0; i < n - 1; ++i) {
        // 🟡 优化标记：记录本轮是否有节点的距离被更新
        // 如果某一轮没有任何更新，说明所有节点的最短距离已经稳定，直接提前退出，减少无效计算
        bool updated = false;

        // 🔵 遍历图中【所有的边】，对每条边执行「松弛操作」
        // Bellman-Ford 是边集驱动的算法，必须遍历所有边，和 Dijkstra 的邻接表驱动不同
        for (const Edge& e : edges) {
            // 🟢 松弛操作的两个前置条件：
            // 条件1：dist[e.from] != INF → 边的起点 e.from 必须是「可达」的（从起点能走到 e.from）
            //        如果 e.from 不可达，那么 e.to 也不可能通过这条边到达，直接跳过，避免无效计算和溢出
            // 条件2：dist[e.from] + e.weight < dist[e.to] → 松弛核心条件
            //        含义：「起点到 e.from 的距离 + 这条边的权值」 < 「当前记录的起点到 e.to 的距离」
            //        说明走 e.from → e.to 这条边，能找到到 e.to 的更短路径，需要更新
            if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
                // 🟠 执行松弛：更新 e.to 的最短距离为更短的路径长度
                dist[e.to] = dist[e.from] + e.weight;

                // 🟡 标记本轮有更新：说明最短路径还没稳定，需要继续下一轮
                updated = true;
            }
        }

        // 🟡 优化：如果本轮没有任何更新，说明所有节点的最短距离已经确定
        // 后面的轮次不会再产生任何更新，直接 break 提前退出，大幅提升效率
        if (!updated) {
            break;
        }
    }

    // ==============================================
    // 3. 负权环检测（Bellman-Ford 独有的核心功能）
    // ==============================================
    // 🔴 原理：
    // 经过 n-1 轮松弛后，所有【无环的最短路径】都已经完全确定，不可能再更新
    // 如果此时还能对某条边执行松弛操作（即还能更新距离），说明图中存在【负权环】
    // 负权环：总权值为负的环，绕环走一圈距离会变小，可无限绕圈，导致最短路径无限小，无意义
    for (const Edge& e : edges) {
        // 同样需要判断 e.from 是否可达：只有从起点可达的负权环，才会影响最短路径
        if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
            // 🔴 仍能松弛 → 存在从起点可达的负权环，返回 false
            return false;
        }
    }

    // ==============================================
    // 4. 算法正常结束：无负权环，最短路径计算完成
    // ==============================================
    return true;
}

// 辅助函数：打印算法结果，方便测试
void printResult(int n, const vector<long long>& dist, bool hasNegativeCycle) {
    if (!hasNegativeCycle) {
        cout << "❌ 检测到从起点可达的负权环，不存在有效最短路径！" << endl;
        return;
    }

    cout << "✅ 无负权环，起点到各节点的最短距离如下：" << endl;
    const long long INF = 1e18;
    for (int i = 0; i < n; ++i) {
        if (dist[i] == INF) {
            cout << "节点 " << i << "：不可达" << endl;
        } else {
            cout << "节点 " << i << "：" << dist[i] << endl;
        }
    }
}

// 主函数：测试用例（覆盖两种核心场景）
int main() {
    // 测试用例1：无负权环（含负权边，验证算法正确性）
    cout << "========== 测试用例1：无负权环 ==========" << endl;
    int n1 = 5; // 5个节点（0~4）
    vector<Edge> edges1;
    // 构建边：(起点, 终点, 权值)
    edges1.emplace_back(0, 1, 2);  // 0→1，权值2
    edges1.emplace_back(0, 2, 5);  // 0→2，权值5
    edges1.emplace_back(1, 2, -1); // 1→2，权值-1（负权边，验证算法）
    edges1.emplace_back(1, 3, 4);  // 1→3，权值4
    edges1.emplace_back(2, 3, 1);  // 2→3，权值1
    edges1.emplace_back(2, 4, 3);  // 2→4，权值3
    edges1.emplace_back(3, 4, 2);  // 3→4，权值2

    vector<long long> dist1;
    bool res1 = bellmanFord(n1, edges1, 0, dist1);
    printResult(n1, dist1, res1);

    // 测试用例2：含负权环（验证负环检测功能）
    cout << "\n========== 测试用例2：含负权环 ==========" << endl;
    int n2 = 4; // 4个节点（0~3）
    vector<Edge> edges2;
    // 构建边：0→1(1)、1→2(-3)、2→1(1) → 构成负权环：1→2→1，总权值-3+1=-2<0
    edges2.emplace_back(0, 1, 1);
    edges2.emplace_back(1, 2, -3);
    edges2.emplace_back(2, 1, 1);
    edges2.emplace_back(1, 3, 2);

    vector<long long> dist2;
    bool res2 = bellmanFord(n2, edges2, 0, dist2);
    printResult(n2, dist2, res2);

    return 0;
}
```

---

## 二、逐模块深度拆解（彻底懂每一行）