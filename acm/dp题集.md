# dp题题集

[TOC]

- 由于dp做一道卡一道，于是开始死磕的日日夜夜

## P1216 数字三角形 Number Triangles

- dp入门，求路径数字和最大

```C++
#include<bits/stdc++.h>
using namespace std;
int dp[1005][1005];//dp[i][j]表示以第i行第j列为终点的最大值
int a[1005][1005];
int ma;
int main()
{
	int r;
	cin>>r;
	for(int i=1;i<=r;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
		dp[i][1]=a[i][1]+dp[i-1][1]; 
	}
	
	
	for(int i=2;i<=r;i++)
	{
		for(int j=2;j<=i;j++)
		{
			dp[i][j]=max(dp[i-1][j-1],dp[i-1][j])+a[i][j];
			if(i==r)
			{
				ma=max(dp[i][j],ma);
			}
		}	
	}
	cout<<ma<<endl;
	
	
	
	return 0;
}
```

- 滚动数组优化

```C++
dp[1]=a[1][1];
for(int i=2;i<=r;i++)
{
	for(int j=i;j>=2;j--) //从后往前，不然会被覆盖
	{
		dp[j]=max(dp[j-1],dp[j])+a[i][j];
		if(i==r)
		{
			ma=max(dp[j],ma);
		}
	}
	dp[1]+=a[i][1];	//更新dp[1]
}
```

[P1216 [USACO1.5\][IOI1994]数字三角形 Number Triangles - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P1216)

## P2196 挖地雷

- 对存路径有点迷茫TAT，看了俩小时

- 注意不能有多余空格不然只有60分

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int a[25],dp[25],path[25][25],p[25];
  //dp[i]表示以i为终点地雷最多的路 
  //p[i]表示最优路径下i的前驱  后续输出路径 
  int n,maxn,maxi;
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		cin>>a[i];
  		dp[i]=a[i];//初始化 
  	}
  	
  	for(int i=1;i<n;i++)
  	{
  		for(int j=i+1;j<=n;j++)
  		{
  			cin>>path[i][j]; //表示i能否通向j 
  		}
  	}
  		
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<i;j++)//important!因为路是单向的，前面输入的路径是第i号地窖往后通，所以这里应该看第i号地窖往前有没有别的地窖通向i
  		{
  			if(path[j][i]!=0&&dp[i]<dp[j]+a[i]) //j能通向i 
  			{
  				dp[i]=dp[j]+a[i];
  				p[i]=j;
  			}	
  		}
  		if(dp[i]>maxn)
  		{
  //			cout<<dp[i]<<endl;
  			maxn=dp[i];
  			maxi=i;
  		}
  	 } 
  	
  	stack<int> s;
  	while(maxi)
  	{
  		s.push(maxi);
  		maxi=p[maxi];
  	}
  	cout<<s.top();
  	s.pop();
  	while(!s.empty())
  	{
  		cout<<" "<<s.top();
  		s.pop();
  	}
  	cout<<endl;
  	cout<<maxn<<endl;
  	return 0;
  }
  ```

  

## P1060 开心的金明

- 01背包问题

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=30010;
  int w[N],v[N],dp[N];
  int m,n;
  int main()
  {
  	cin>>n>>m;
  	for(int i=1;i<=m;i++)
  	cin>>v[i]>>w[i];
  	
  	for(int i=1;i<=m;i++)
  	{
  		for(int j=n;j>=1;j--)
  		{
  			if(j>=v[i])
  			dp[j]=max(dp[j],dp[j-v[i]]+v[i]*w[i]);
  		}
  	}
  	cout<<dp[n]<<endl;
  	return 0;
  }
  ```

  

## P8707 [蓝桥杯 2020 省 AB1] 走方格

- dp基础题捏，求到某个格子最多多少条路

```C++
#include<bits/stdc++.h>
using namespace std;
int m,n;
int dp[35][35];//到第i行第j列有多少条路 
int main()
{
	cin>>n>>m;
	
	for(int i=1;i<=m;i++)
	dp[1][i]=1;
	
	for(int i=1;i<=n;i++)
	dp[i][1]=1;
	
	for(int i=2;i<=n;i++)
	{
		for(int j=2;j<=m;j++)
		{
			if(i%2!=0||j%2!=0)
			dp[i][j]=dp[i-1][j]+dp[i][j-1];
		}
	}
	
	cout<<dp[n][m]<<endl;
	return 0;
}
```

















