
![[Pasted image 20260604174227.png]]
# 代码
```cpp
#include<iostream>
using namespace std;

const int SIZE = 9;
int parent[SIZE];

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
		parent[y] = x;
	}
}

int main()
{
	//数组初始化，存储当前节点自己的编号
	for (int i = 0; i < SIZE; ++i)
	{
		parent[i] = i;
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