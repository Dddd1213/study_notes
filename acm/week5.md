# Week 5

## P8604 [蓝桥杯 2013 国 C] 危险系数

- 知识点：图的vector存储和+dfs
-  思路：用一个数组来记录每个节点被访问的次数，如果起点和终点之间有点的访问次数和终点的访问次数一样，那么它就是关键点。

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=10005;
int m,n,u,v,tot=0,ans=0;//tot用来记录u到v的路径总条数 
int cnt[N]; //cnt[i]表示点i用到的次数 
bool vis[N];
vector<int> G[N];
void dfs(int now)
{
	if(now==v)
	{
		tot++;//如果这条路能到达终点，路径++
		for(int i=1;i<=n;i++)//遍历经过的所有的点，经过的点++
		{
			if(vis[i])
			cnt[i]++;
		}
		return;
	}
	for(int i=0;i<G[now].size();i++)
	{
		int to=G[now][i];
		if(!vis[to])
		{
			vis[to]=true;
			dfs(to);
			vis[to]=false;
		}
	}
	return;
}
int main()
{
	cin>>n>>m;
	while(m--)
	{
		int x,y;
		cin>>x>>y;
		G[x].push_back(y);
		G[y].push_back(x);
	}
	cin>>u>>v;
	vis[u]=true;
	dfs(u);
	for(int i=1;i<=n;i++)
	{
		if(tot==cnt[i])//说明从u到v的每条路都会经过它
		ans++;
	}
	ans-=2;//去掉u点和v点
	cout<<ans<<endl;
	return 0;
 } 
```

## P3916 图的遍历

-  知识点：反向建图+dfs

- 原思路：直接建图dfs搜每一条，用ma记录能到的点并逐一比较，最后用ans[i]记录第i个点能到的最大点

  错因：此时n的最大范围已经达到了1e5，如果用n方的暴力算法是过不去的（超时QWQ

-  改进思路：反着建边然后从n（最大点）开始倒着枚举每一个点，并标记能搜到的所有点。如果这个点已经被标记过了，就说明有更大的点能到达它。

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=100010;
int vis[N];
vector<int> G[N];
void dfs(int now,int y) //从y出发能到的点 
{
	if(vis[now])
	return ;
	vis[now]=y;
	for(int i=0;i<G[now].size();i++)
	dfs(G[now][i],y);
}
int main()
{
	int m,n;
	cin>>n>>m;
	while(m--)
	{
		int x,y;
		cin>>x>>y;
		G[y].push_back(x);
	}
	for(int i=n;i>0;i--)
	dfs(i,i);
	for(int i=1;i<=n;i++)
	cout<<vis[i]<<" ";
	return 0;
}
```

##  P1330 封锁阳光大学

-  知识点：染色+dfs
-  思路：
  - 道路被封锁的条件：每条路**有且仅有**一端有河蟹（因为河蟹不和谐），所以可以考虑将相邻的点染成不同颜色以表示有无河蟹。
  - 方法：遍历至少一端已染色的每条边，若另一端没有染色，则染上和这一端**相反**的颜色；如果都已经染色，**判断是否相反**，若不相反就Impossible。
- 注意：
  - Q：为什么不用回溯？A：只有在需要求出所有可能路径的题中才需要回溯，该题只是为了把所有结点走一遍。
  - 该题可能是非连通图！！

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=100010;
int ans=0;
bool vis[N];//记录是否到达过 
int col[N]={0};//1为白，2为黑
int sum[3]={0};//黑白染色的点数 1为白，2为黑 
vector<int> G[N];
bool dfs(int now,int color)
{
	if(vis[now]) //如果已经染色 
	{
		if(col[now]==color) return true;
		return false;
	}
	vis[now]=true;
	col[now]=color;
	sum[color]++;
	for(int i=0;i<G[now].size();i++)
	{
		if(!dfs(G[now][i],color%2+1))
		return false; //这里要多加一个return
	}
	return true;
 } 
int main()
{
	int n,m;
	cin>>n>>m;
	while(m--)
	{
		int u,v;
		cin>>u>>v;
		G[u].push_back(v);
		G[v].push_back(u);
	}
	memset(vis,0,sizeof(vis));
	for(int i=1;i<=n;i++)
	{
		if(vis[i]) continue;
		memset(sum,0,sizeof(sum));
		memset(col,0,sizeof(col));
		if(!dfs(i,1))
		{
			cout<<"Impossible"<<endl;
			return 0;
		 } 
		 ans+=min(sum[1],sum[2]);
	 } 
	 cout<<ans<<endl;
	return 0; 
}
```

