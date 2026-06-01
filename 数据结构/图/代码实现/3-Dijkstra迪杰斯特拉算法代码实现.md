[3Dijkstra算法代码实现讲解_ev_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Zy411q7Lq?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=16)

```cpp
#include<iostream>
#include<vector>
using namespace std;
using uint = unsigned int;
const uint INF = INT_MAX;

//迪杰斯特拉算法接口
int Dijkstra(vector<vector<uint>>& graph,
    int start, //起点
    int end) //终点
{
    const int N = graph.size();

    //存储各个顶点的最短路径（最小权值）
    vector<uint>dis(graph.size(), 0);
    //存储各个顶点是S集合还是在U集合中
    vector<bool>use(graph.size(), false);
    

    //把start放入S集合中
    use[start] = true;
    //初始化start节点到其他U集合顶点权值
    for (int i = 0; i < N; ++i)
    {
        dis[i] = graph[start][i];
    }

    //把U集合中的定点处理完
    //因为已经处理了一个了，因此i从1开始，少循环一次
    for (int i = 1; i < N; ++i)
    {
        //先从U集合中找到权值最小的顶点
        int k = -1; //存下标
        int min = INF; //存权值
        for (int j = 0; j < N; ++j)
        {
	        //找没有放到S集合中的U集合中的值最小的
            if (!use[j] && dis[j] < min)
            {
                min = dis[j];
                k = j;
            }
        }

        if (k == -1)
        {
            break;
        }

        //把选出的顶点放入到S集合中
        use[k] = true;

        //把U集合中剩余顶点的权值信息更新一下
        for (int j = 0; j < N; ++j)
        {
            if (!use[j] && min + graph[k][j] < dis[j])
            {
                dis[j] = min + graph[k][j];
            }
        }
    }

    //测试打印
    for (int d : dis)
    {
        cout << d << " ";
    }
    cout << endl;

    return dis[end];
}
int main()
{
    vector<vector<uint>> graph =
    {
        {0, 6, 3, INF, INF, INF},
        {6, 0, 2, 5, INF, INF},
        {3, 2, 0, 3, 4, INF},
        {INF, 5, 3, 0, 2, 3},
        {INF, INF, 4, 2, 0, 5},
        {INF, INF, INF, INF, INF, INF},
    };
    
    int distance = Dijkstra(graph, 0, 2);
    if (distance == INF)
    {
        cout << "不存在有效路径！" << endl;
    }
    else
    {
        cout << "distance:" << distance << endl;
    }
}
```


# 一、时间复杂度（Time Complexity）

## 最终结论

**O(N²)**

（N 是图的顶点数量）

---

## 详细推导（对应你代码的循环）

你的代码结构：

1. **外层大循环**：遍历 N 个顶点
    
    `for (int i = 1; i < N; ++i)`
    
    → **执行 N 次**
    
2. **内层第一个循环**：找最小值 min
    
    `for (int j = 0; j < N; j++)`
    
    → **每次执行 N 次**
    
3. **内层第二个循环**：松弛更新距离
    
    `for (int j = 0; j < N; j++)`
    
    → **每次执行 N 次**
    

---

### 总次数

N × (N + N) = **O(N²)**

---

# 二、空间复杂度（Space Complexity）

## 最终结论

**O(N²)**

---

## 详细推导

你开辟了这些空间：

1. **邻接矩阵 graph**：N × N 大小
    
    → **O(N²)**
    
2. **距离数组 dis**：N 个元素
    
    → **O(N)**
    
3. **标记数组 use**：N 个元素
    
    → **O(N)**
    

---

### 总空间复杂度

取最大项：

**O(N²)**

---

# 三、最终标准答案

- **时间复杂度：O (N²)**
- **空间复杂度：O (N²)**

