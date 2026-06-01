```cpp
#include <iostream>
#include <vector>
#include <climits>
#include <algorithm>
using namespace std;

using uint = unsigned int;
const uint INF = INT_MAX;

int main()
{
    vector<vector<uint>> graph =
    {
        {0, 6, 3, INF, INF, INF},
        {6, 0, 2, 5, INF, INF},
        {3, 2, 0, 3, 4, INF},
        {INF, 5, 3, 0, 2, 3},
        {INF, INF, 4, 2, 0, 5},
        {INF, INF, INF, 3, 5, 0},
    };

    // 循环变量用 size_t，和 graph.size() 类型匹配
    for (size_t k = 0; k < graph.size(); k++)
    {
        for (size_t i = 0; i < graph.size(); i++)
        {
            for (size_t j = 0; j < graph.size(); j++)
            {
                if (graph[i][k] != INF && graph[k][j] != INF)
                {
                    graph[i][j] = min(graph[i][j], graph[i][k] + graph[k][j]);
                }
            }
        }
    }

    for (const auto& line : graph)
    {
        for (uint dis : line)
        {
            cout << dis << " ";
        }
        cout << endl;
    }
}
```