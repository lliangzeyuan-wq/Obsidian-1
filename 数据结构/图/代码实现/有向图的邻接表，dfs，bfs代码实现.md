[7有向图的邻接表代码实现_ev_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Zy411q7Lq?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=13)

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<list>
#include<vector>
#include<queue>
#include <cstring>
using namespace std;

class Digraph
{
public:
	// 从配置文件读入顶点和边的信息，生成邻接表
	// 文件格式：顶点名一行，边列表一行，交替出现  
	void readFile(string filePath)
	{
		FILE* pf = fopen(filePath.c_str(), "r");
		if (pf == nullptr)
		{
			throw filePath + " not exists!";
		}

		// 占用第0号位置，让顶点从1开始编号
		vertics.emplace_back("");

		char line[1024] = { 0 };
		while (true)
		{
			// 1. 读顶点名称
			if (fgets(line, 1024, pf) == nullptr) break;
			// 去掉换行符
			line[strcspn(line, "\n")] = 0;
			vertics.emplace_back(line);

			// 2. 读边列表
			if (fgets(line, 1024, pf) == nullptr) break;
			line[strcspn(line, "\n")] = 0;

			// 3. 分割边列表并存入邻接表
			char* vertic_no = strtok(line, ",");
			while (vertic_no != nullptr)
			{
				int vex = atoi(vertic_no);
				if (vex > 0)
				{
					vertics.back().adjList_.emplace_back(vex);
				}
				vertic_no = strtok(nullptr, ",");
			}
		} 

		fclose(pf);
	}

	void show() const
	{
		for (int i = 1; i < vertics.size(); i++)
		{
			cout << vertics[i].data_ << " : ";
			for (auto no : vertics[i].adjList_)
			{
				cout << no << " ";
			}
			cout << endl;
		}
		cout << endl;
	}

	//图的深度优先遍历
	void dfs()
	{
		vector<bool>visited(vertics.size(), false);
		dfs(1, visited);
		cout << endl;
	}

	//图的广度优先遍历
	void bfs()
	{
		vector<bool>visited(vertics.size(), false);
		queue<int>que;
		que.push(1);
		visited[1] = true;
		while (!que.empty())
		{
			int cur_no = que.front();
			que.pop();
			
			cout << vertics[cur_no].data_ << " ";

			for (auto no : vertics[cur_no].adjList_)
			{
				if (!visited[no])
				{
					que.push(no);
					visited[no] = true;
				}
			}
		}
		cout << endl;
	}

private:
	//深度优先遍历的递归接口
	void dfs(int start, vector<bool>& visited)
	{
		//该start顶点已经遍历过了
		if (visited[start])
		{
			return;
		}
		cout << vertics[start].data_ << " ";
		visited[start] = true;
		
		//递归遍历下一层节点
		for (auto no : vertics[start].adjList_)
		{
			dfs(no, visited);
		}
	}



private:
	struct Vertic
	{
		Vertic(string data)
			:data_(data)
		{
		}
		string data_;
		list<int> adjList_;
	};
	 
	vector<Vertic> vertics;
};

int main()
{
	try {
		Digraph graph;
		graph.readFile("data.txt");
		graph.show();
		graph.dfs();
		graph.bfs();
	}
	catch (const string& err) {
		cout << err << endl;
	}
	return 0;
}




```

```data.txt
A
2,4,5
B
6
C
2,4
D
3,8
E
8
F
7,9
G
2,3
H
0
i
8
```