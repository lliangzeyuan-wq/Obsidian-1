---
data: 2026-03-01
---
- sort默认排序是按照pair<> 的第一个数据来排序的
```cpp
vector<pair<int,int>>v;
sort(v.begin(),v.end());
```



- 如果想要按照“右端点”排序，需要手动写比较规则
```cpp
vector<pair<int,int>>v;
sort(v.begin(),v.end(),[](const pair<int,int>&a,const pair<int,int>&b){return a.second<b.second;})
```