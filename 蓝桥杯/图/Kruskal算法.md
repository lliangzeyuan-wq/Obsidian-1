---
data: 2026-04-03
---
![[Pasted image 20260403214434.png]]

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// ==================== 排序用的比较函数（不用lambda，零乱码） ====================
bool cmp(const vector<int>& a, const vector<int>& b) {
    return a[2] < b[2]; // 按道路长度从小到大排序
}

// ==================== 并查集类（完整实现） ====================
class UF {
private:
    int count;          // 连通分量个数
    vector<int> parent; // 父节点数组
    vector<int> size;   // 树的大小，用于按秩合并

    // 找根节点，带路径压缩
    int find(int x) {
        while (parent[x] != x) {
            parent[x] = parent[parent[x]]; // 路径压缩
            x = parent[x];
        }
        return x;
    }

public:
    // 构造函数：初始化1~n的城市节点
    UF(int n) {
        count = n;
        parent.resize(n + 1); // 城市编号1~n，0号闲置不用
        size.resize(n + 1, 1);
        for (int i = 1; i <= n; ++i) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    // 合并两个连通分量
    void unite(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ) return; // 已经连通，无需合并

        // 小树挂到大树上，保持树平衡
        if (size[rootP] > size[rootQ]) {
            parent[rootQ] = rootP;
            size[rootP] += size[rootQ];
        } else {
            parent[rootP] = rootQ;
            size[rootQ] += size[rootP];
        }
        count--; // 连通分量数减1
    }

    // 判断两个节点是否连通
    bool connected(int p, int q) {
        return find(p) == find(q);
    }

    // 获取当前连通分量总数
    int getCount() {
        return count;
    }
};

// ==================== 主函数（完整逻辑） ====================
int main() {
    // 输入加速，避免大数据超时
    ios::sync_with_stdio(false);
    cin.tie(NULL);

    int T; // 测试用例数量
    cin >> T;
    while (T--) {
        int N, M; // N：城市数，M：道路数
        cin >> N >> M;
        vector<vector<int>> edges; // 存储所有道路
        edges.reserve(M); // 提前分配空间，提升效率

        // 读取所有道路
        for (int i = 0; i < M; ++i) {
            int X, Y, C;
            cin >> X >> Y >> C;
            // 把道路存入数组，兼容所有版本Dev-C++
            vector<int> temp;
            temp.push_back(X);
            temp.push_back(Y);
            temp.push_back(C);
            edges.push_back(temp);
        }

        // ==================== 修复后的排序代码（零乱码，不用lambda） ====================
        sort(edges.begin(), edges.end(), cmp);

        UF uf(N); // 初始化并查集
        int max_fuel = 0; // 记录最小生成树的最大边（答案）

        // Kruskal算法核心：遍历排序后的边
        for (int i = 0; i < M; ++i) {
            int X = edges[i][0];
            int Y = edges[i][1];
            int C = edges[i][2];

            // 如果已经连通，跳过（避免成环）
            if (uf.connected(X, Y)) {
                continue;
            }

            // 合并连通分量，更新最大边
            uf.unite(X, Y);
            if (C > max_fuel) {
                max_fuel = C;
            }

            // 所有城市已经连通，提前退出循环
            if (uf.getCount() == 1) {
                break;
            }
        }

        // 输出当前测试用例的答案
        cout << max_fuel << endl;
    }
    return 0;
}
```
