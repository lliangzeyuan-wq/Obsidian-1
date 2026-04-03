---
data: 2026-04-02
---
![[Pasted image 20260403144047.png]]

## 代码说明
- 完全适配题目要求：**负权边 + 全源最短路径 + 负权环检测 + 不可达赋值 1e9 + 按公式求和**
- 带**逐行超详细注释**，新手也能看懂
- 数据类型全用 `long long`，杜绝溢出
- 节点编号：**1~n**（和题目一致），虚拟源点用 `0`

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
#include <algorithm>
using namespace std;

// 数据类型别名：简化代码，ll 代表 long long
typedef long long ll;
typedef pair<ll, int> PLI;  // Dijkstra 优先队列元素：<距离, 节点编号>

// 题目常量定义
const ll INF_DIST = 1e18;    // 算法内部用的无穷大（计算最短路径）
const ll INF_REACH = 1e9;   // 题目规定：不可达节点的距离赋值 1e9
const int MAXN = 1005;      // 节点最大数量（题目 n≤1e3）

// 边结构体：存储 起点、终点、权值（Bellman-Ford 用）
struct Edge {
    int from, to;
    ll weight;
    Edge(int f, int t, ll w) : from(f), to(t), weight(w) {}
};

vector<Edge> edges;          // 存储原图所有边
vector<pair<int, ll>> adj[MAXN]; // 邻接表：Dijkstra 用（存<终点, 新权值>）
ll h[MAXN];                 // 势函数 h[u]：由 Bellman-Ford 求出
int n, m;                   // n=节点数，m=边数

/**
 * @brief Bellman-Ford 算法：求势函数 h[] + 检测负权环
 * @param start 虚拟源点（固定为 0）
 * @return true=无负权环，false=存在负权环
 */
bool bellmanFord(int start) {
    // 1. 初始化：势函数 h[] 全设为无穷大
    fill(h, h + MAXN, INF_DIST);
    h[start] = 0;  // 虚拟源点到自己的距离为 0

    // 2. 核心：n 轮松弛（虚拟源点+原图n个节点，总节点数 n+1，松弛 n 轮）
    for (int i = 0; i < n; ++i) {
        bool updated = false;
        // 遍历所有边（原图 + 虚拟源点的边）
        for (const Edge& e : edges) {
            int u = e.from, v = e.to;
            ll w = e.weight;
            // 松弛操作：h[v] > h[u] + w → 更新
            if (h[u] != INF_DIST && h[v] > h[u] + w) {
                h[v] = h[u] + w;
                updated = true;
            }
        }
        // 无更新，提前退出
        if (!updated) break;
    }

    // 3. 负权环检测：第 n+1 轮松弛，若还能更新 → 存在负权环
    for (const Edge& e : edges) {
        int u = e.from, v = e.to;
        ll w = e.weight;
        if (h[u] != INF_DIST && h[v] > h[u] + w) {
            return false;
        }
    }
    return true;
}

/**
 * @brief 堆优化 Dijkstra 算法：求新权图的单源最短路
 * @param start 起点
 * @return 起点到所有节点的【新权图最短距离】
 */
vector<ll> dijkstra(int start) {
    // 初始化距离数组
    vector<ll> dist(n + 1, INF_DIST);
    dist[start] = 0;
    // 优先队列（小根堆）：<距离, 节点>，默认大根堆，用 greater 转小根堆
    priority_queue<PLI, vector<PLI>, greater<PLI>> q;
    q.push({0, start});

    while (!q.empty()) {
        auto [d, u] = q.top();
        q.pop();

        // 已找到更短路径，跳过
        if (d > dist[u]) continue;

        // 遍历邻接边
        for (auto [v, w] : adj[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                q.push({dist[v], v});
            }
        }
    }
    return dist;
}

int main() {
    // 加速 cin/cout（竞赛必备，防止超时）
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    // ===================== 1. 输入数据 =====================
    cin >> n >> m;
    // 读入 m 条有向边
    for (int i = 0; i < m; ++i) {
        int u, v;
        ll w;
        cin >> u >> v >> w;
        // 存入边结构体
        edges.emplace_back(u, v, w);
    }

    // ===================== 2. 添加虚拟源点 =====================
    // 虚拟源点 0 → 所有节点 1~n，权值为 0
    for (int i = 1; i <= n; ++i) {
        edges.emplace_back(0, i, 0);
    }

    // ===================== 3. Bellman-Ford 求势函数 + 负环检测 =====================
    if (!bellmanFord(0)) {
        // 存在负权环：直接输出 -1，结束程序
        cout << -1 << endl;
        return 0;
    }

    // ===================== 4. 重赋权：将负权边转为非负权边 =====================
    // 用公式：新权值 = 原权值 + h[u] - h[v]
    for (const Edge& e : edges) {
        int u = e.from, v = e.to;
        // 跳过虚拟源点的边
        if (u == 0) continue;
        ll new_w = e.weight + h[u] - h[v];
        // 加入邻接表（Dijkstra 用）
        adj[u].emplace_back(v, new_w);
    }

    // ===================== 5. 对每个节点跑 Dijkstra，计算全源最短路 =====================
    for (int u = 1; u <= n; ++u) {
        // 跑 Dijkstra，得到新权图的最短距离
        vector<ll> new_dist = dijkstra(u);
        ll ans = 0;  // 存储当前节点 u 的答案

        // 遍历所有终点 j，还原真实距离并计算
        for (int j = 1; j <= n; ++j) {
            ll real_dist;
            if (new_dist[j] == INF_DIST) {
                // 不可达：按题目要求赋值 1e9
                real_dist = INF_REACH;
            } else {
                // 可达：还原真实距离 → 公式：真实距离 = 新距离 - h[u] + h[j]
                real_dist = new_dist[j] - h[u] + h[j];
            }
            // 按公式累加：ans += j * 真实距离
            ans += (ll)j * real_dist;
        }

        // 输出当前节点的答案
        cout << ans << endl;
    }

    return 0;
}
```
