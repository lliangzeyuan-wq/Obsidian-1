# 优化思路

- 引入rank记录层高

![[Pasted image 20260604175933.png]]




![[Pasted image 20260604175852.png]]
# 问题
- find优化之后，每次调用find的时候，层高可能会变化，但是我们的代码里面rank没有相应的跟着变化。因此find
# 代码
```cpp
#include<iostream>
using std::cout;
using std::cin;
using std::endl;

const int SIZE = 9;
int parent[SIZE]; //记录每个节点的父节点
int rank[SIZE]; //记录节点的层高

//并查集-查询方法。返回x节点所在的树的根节点  
int non_find(int x)
{
	while (x != parent[x])
	{
		x = parent[x];
	}
	return x;
}

//递归版本实现
int find(int x)
{
	if (x == parent[x])
	{
		return x;
	}
	//执行顺序等价于：
	/*
	int root = find(parent[x]);
	parent[x] = root;
	return root;
	*/
	return parent[x] = find(parent[x]);
}

//并查集-union合并方法
//x和y原来不在一个集合中，才需要合并
void merge(int x,int y)
{
	x = find(x);
	y = find(y);
	if (x != y)
	{
		if (rank[x] > rank[y])
		{
			parent[y] = x;
		}
		else
		{
			if (rank[x] == rank[y])
			{
				rank[y]++;
			}
			parent[x] = y;
		}

	}
}

int main()
{
	//数组初始化，存储当前节点自己的编号
	for (int i = 0; i < SIZE; ++i)
	{
		parent[i] = i;
		rank[i] = 1;  
	}

	int x, y;
	for (int i = 0; i < 6; ++i)
	{
		cin >> x >> y;
		merge(x, y);
	}
	
	cout << (find(1) == find(4) ? "OK" : "NO") << endl;
	return 0;
}

/*
1 3
1 2
5 4
2 4
6 8
8 7
*/
```