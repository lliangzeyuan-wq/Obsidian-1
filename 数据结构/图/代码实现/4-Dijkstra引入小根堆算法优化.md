- 引入小根堆，更新的时候无法“进入”小根堆操作，因此需要重复添加，加入use[]检测 , 因此这个时间复杂度不一定是优化

```cpp
#include<iostream>
#include<vector>
#include<queue>
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

    //定义小根堆 pair<权值，下标>
    priority_queue<pair<uint, int>, vector<pair<uint, int>>, greater< pair<uint, int>>>que;

    //把start放入S集合中
    use[start] = true;
    //初始化start节点到其他U集合顶点权值
    for (int i = 0; i < N; ++i)
    {
        dis[i] = graph[start][i];
        //把除start顶点的其他顶点全部放入U集合小根堆中
        if (i != start)
        {
            que.emplace(graph[start][i], i);
        }
    }

    //把U集合中的定点处理完
    while(!que.empty())
    {

        auto pair = que.top();
        que.pop();
        if (pair.first == INF)
        {
            break;
        }
        int k = pair.second;
        int min = pair.first;


        if (use[k])
            continue;

        //把选出的顶点放入到S集合中
        use[k] = true;

        //把U集合中剩余顶点的权值信息更新一下
        for (int j = 0; j < N; ++j)
        {
            if (!use[j] && min + graph[k][j] < dis[j])
            {
                dis[j] = min + graph[k][j];
                //我们无法直接对小根堆中的元素进行操作，需要重复添加
                //当他是最小的时候（位于栈顶），这个时候从栈取出来的时候
                //，可能是已经用过的，这个时候continue一下
                que.emplace(dis[j], j);
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