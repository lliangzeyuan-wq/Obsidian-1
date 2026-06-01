# 多源最短路径算法 - Floyd 算法

Floyd 算法又称为插点法，是一种利用动态规划的思想寻找给定的加权图中多源点之间最短路径的算法，主要思想是：

1. 从第 1 个点到第 n 个点依次加入图中，每个点加入后进行试探是否有路径长度被更改。具体方法为遍历图中每一个点 (i,j 双重循环)，判断每一个点对距离是否因为加入的点而发生最小距离变化。如果发生改变，更新两点 (i,j) 的距离。
2. 重复上述直到最后插点试探完成。

其中更新距离的状态转移方程为：

`dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j])`

其中`dp[x][y]`的意思可以理解为 x 到 y 的最短路径。`dp[i][k]`为 i 到 k 的最短路径，`dp[k][j]`为 k 到 j 的最短路径。

---

### 附：可直接复制的 C++ 模板

cpp

运行

```
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

const int INF = INT_MAX / 2; // 防止溢出

void Floyd(vector<vector<int>>& dp, int n) {
    // 初始化：dp[i][j] = i到j的直接边权
    for (int k = 0; k < n; ++k) {          // 中间点
        for (int i = 0; i < n; ++i) {      // 起点
            for (int j = 0; j < n; ++j) {  // 终点
                if (dp[i][k] < INF && dp[k][j] < INF) {
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j]);
                }
            }
        }
    }
}

int main() {
    int n = 4; // 顶点数
    vector<vector<int>> dp(n, vector<int>(n, INF));
    // 自己到自己距离为0
    for (int i = 0; i < n; ++i) dp[i][i] = 0;

    // 示例边权（可替换为你的数据）
    dp[0][1] = 2; dp[0][2] = 6; dp[0][3] = 4;
    dp[1][2] = 3; dp[1][0] = 2;
    dp[2][0] = 6; dp[2][1] = 3; dp[2][3] = 1;
    dp[3][0] = 4; dp[3][2] = 1;

    Floyd(dp, n);

    // 打印所有点对的最短路径
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            if (dp[i][j] >= INF) cout << "INF ";
            else cout << dp[i][j] << " ";
        }
        cout << endl;
    }
    return 0;
}
```