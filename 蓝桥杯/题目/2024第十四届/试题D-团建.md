---
data: 2026-03-09
---
- 对于树的结点关系，用邻接表(无向图类型的)来表示
- 同时dfs两棵树

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+10;
int a[N],b[N],ans;
vector<int>GN[N],GM[N];
//	dfs(1,0,1,0,1);
void dfs(int pN,int faN,int pM,int faM,int dep)
{
	ans=max(ans,dep);
	map<int,int>bk;
	for(auto sonN:GN[pN])
	{
		if(sonN!=faN) bk[a[sonN]]=sonN;
	} 
	for(auto sonN:GN[pN])
	{  
		if(sonN!=faN)
		{
			if(bk.count(b[sonM]))
			{
				dfs(bk[b[sonM]],pN,sonM,pM,dep+1);
			}
		}
	}
}
int main()
{
	int n,m,x,y;
	cin>>n>>m;
	for(int i=1;i<=n;++i)cin>>a[i];
	for(int i=1;i<=m;++i)cin>>b[i];
	for(int i=1;i<n;++i)
	{
		cin>>x>>y;
		GN[x].push_back(y);
		GN[y].push_back(x);
	}
	for(int i=1;i<m;++i)
	{
		cin>>x>>y;
		GM[x].push_back(y);
		GM[y].push_back(x);
	}
	if(a[1]!=b[1])
	{
		cout<<0;
		return 0;
	}
	
	dfs(1,0,1,0,1);
	cout<<ans;
	return 0;
}
```