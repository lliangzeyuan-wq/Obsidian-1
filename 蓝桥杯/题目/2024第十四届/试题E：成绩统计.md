---
data: 2026-03-09
---
- 为什么用二分？这一点可以想一想，不行问ai
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N=10e5+10;
	int n,k,T;
int a[N];
int b[N];
int pre1[N],pre2[N];
int check(int x)
{
	for(int i=1;i<=x;++i)
	{
		b[i]=a[i];
	}
	sort(b+1,b+x+1);
	
	for(int i=1;i<=x;++i)
	{
		pre1[i]=pre1[i-1]+b[i];
		pre2[i]=pre2[i-1]+b[i]*b[i];
	}
	
	for(int i=1;i+k-1<=x;++i)
	{
		int j=i+k-1;
		int sum_v = pre1[j] - pre1[i - 1];    // ∑v_i
        int sum_v2 = pre2[j] - pre2[i - 1]; // ∑v_i²
        
        // 方差公式展开：k*sum_v2 - (sum_v)^2 < k*k*t
        int s = k * sum_v2 - sum_v * sum_v;
        
        if (s < k * k * T)
            return 1; // 找到满足条件的k个数
	}
	
	return 0;
}
signed main()
{

	cin>>n>>k>>T;
	for(int i=1;i<=n;++i) cin>>a[i];
	int ans=-1,l=k,r=n;
	while(l<=r)
	{
		int mid=(l+r)/2;
		if(check(mid))
		{
			r=mid-1;
			ans=mid;
		}
		else
		{
			l=mid+1;
		}
	}
	
	cout<<ans<<endl;
	return 0;
}
```