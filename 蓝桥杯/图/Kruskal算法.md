---
data: 2026-04-03
---
## 版本 1：算法题标准全局数组版（推荐竞赛用）

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// -------------------------- 1. 边的结构体 --------------------------
// 存储无向边：起点u、终点v、权值w
struct Edge {
    int u, v, w;
    // 重载<运算符，让sort直接按权值从小到大排序
    bool operator<(const Edge& other) const {
        return this->w < other.w;
    }
};

// -------------------------- 2. 并查集（带路径压缩+按秩合并优化） --------------------------
const int MAXN = 100010; // 最大顶点数，可根据题目调整（比如n=1e5就设1e5+10）
int father[MAXN]; // 父节点数组
int rank_[MAXN];  // 秩数组（记录树的高度，用于按秩合并，避免树过高）

// 初始化并查集：每个节点的父节点是自己，秩为1
void init(int n) {
    for (int i = 1; i <= n; ++i) { // 1-based顶点编号，和你之前的代码习惯一致
        father[i] = i;
        rank_[i] = 1;
    }
}

// 查找根节点（带路径压缩：让x直接指向根，加速后续查询）
int find(int x) {
    if (father[x] != x) {
        father[x] = find(father[x]);
    }
    return father[x];
}

// 合并两个集合（按秩合并：保证树的平衡，时间复杂度接近O(1)）
// 返回值：true=合并成功（选这条边），false=已连通（跳过这条边）
bool unite(int x, int y) {
    int fx = find(x);
    int fy = find(y);
    if (fx == fy) return false; // 两个节点已经连通，加边会成环，跳过
    
    // 把矮树合并到高树的根下，保持树的平衡
    if (rank_[fx] > rank_[fy]) {
        father[fy] = fx;
    } else {
        father[fx] = fy;
        if (rank_[fx] == rank_[fy]) {
            rank_[fy]++;
        }
    }
    return true;
}

// -------------------------- 3. Kruskal算法主函数 --------------------------
int main() {
    ios::sync_with_stdio(false); // 加速cin/cout，避免算法题超时
    cin.tie(nullptr);

    int n, m; // n：顶点数，m：边数
    cin >> n >> m;

    vector<Edge> edges(m); // 存储所有边
    for (int i = 0; i < m; ++i) {
        cin >> edges[i].u >> edges[i].v >> edges[i].w;
    }

    // 步骤1：按边权从小到大排序（贪心核心：先选权值最小的边）
    sort(edges.begin(), edges.end());

    // 步骤2：初始化并查集，每个节点初始为独立集合
    init(n);

    long long total_weight = 0; // 最小生成树总权值（用long long避免int溢出！）
    int edge_count = 0;          // 已选边数，最终需要等于n-1（生成树的边数=顶点数-1）
    // 可选：如果需要求「最小生成树的最大边权」（最小瓶颈生成树），加这个变量
    // long long max_edge = 0;

    // 步骤3：遍历所有边，贪心选边
    for (const Edge& e : edges) {
        // 用并查集判断u和v是否连通：不连通就选这条边
        if (unite(e.u, e.v)) {
            total_weight += e.w; // 累加总权值
            edge_count++;        // 已选边数+1
            // max_edge = max(max_edge, (long long)e.w); // 可选：更新最大边权

            // 优化：选够n-1条边后直接退出，无需遍历剩余边
            if (edge_count == n - 1) {
                break;
            }
        }
    }

    // 步骤4：判断是否生成最小生成树（图是否连通）
    if (edge_count == n - 1) {
        cout << "最小生成树总权值为: " << total_weight << endl;
        // cout << "最小生成树的最大边权为: " << max_edge << endl; // 可选输出
    } else {
        cout << "图不连通，不存在最小生成树！" << endl;
    }

    return 0;
}
```
