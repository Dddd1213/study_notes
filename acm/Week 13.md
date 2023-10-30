# Week 13 

# div2每日一题

[TOC]



## 101. 一个小整数（回）

- 又看题解了，然后代码又没看懂、、

-  思路：任意挑数组合时：假设某个数是最多的，有maxx个，剩下的总共sum个。有4种情况。

  1.maxx 大于 sum+1时，那么一定不成立，把剩下的数全插入最多的数的缝中也填不满，一定会有两个数相邻。所以该情况无法符合题意。

  2.maxx 等于 sum+1时，那么只能将maxx的数两端都占有。假设有maxx个的数是x，其他的是o, 就只能 xoxoxoxox 这样排。

  3.maxx 等于 sum   时，那么x只要占有一端就行，可以是：xoxoxoxoxo或者oxoxoxoxox。

  4.maxx 小于 sum   时，那么就可以随意插了，只要插的时候不重复即可。

-  注意：每次插入数据之后，空位情况都会发生变化

  ```C++
  #include <bits/stdc++.h>
  using namespace std;
  #define int long long
  const int mod=1e9+7;
  const int N=100;
  int v[N];
  bool dfs(vector<int> &res)
  {
      int sum=0,maxx=0,ans=-1;
      for(int i=0;i<10;i++)
      {
          if(v[i]>maxx) //找出最多的数
          {
              maxx=v[i];
              ans=i;
          }
          sum+=v[i];
      }
      if(sum==0)return 1;
      sum-=maxx;//剩下的数有多少个
      if(maxx>sum+1)return false; //情况1
   
      if(maxx==sum+1)//情况2,此时ans必须两端占
      {
          if(ans==0&&res.empty())//如果ans是数字0且res里还没存数字
          {
              return false;
          }
          else
          {
              res.push_back(ans);//？？？没看懂
              v[ans]--;
          }
      }
      else
      {
          int u= res.size()==0 ? 1:0;
          int k= u==1 ? -1:res.back();
   
          for(int i=u;i<10;i++)
          {
              if(v[i] && i!=k)
              {
                  res.push_back(i);
                  v[i]--;
                  break;
              }
          }
      }
      return dfs(res);
  }
   
  signed main() {
      int n=10;
      int sum=0;
      for(int i=0;i<10;i++)
      {
          cin>>v[i];
          sum+=v[i];
      }
      if(v[0]==1&&sum==1)
      {
          cout<<"0"<<endl;
          return 0;
      }
   
      vector<int>res;
      if(dfs(res))
      {
          if(res.size()==0)cout<<-1<<endl;
          else
          {
              for(auto x:res)
                  cout<<x;
              cout<<endl;
          }
      }
      else
      {
          cout<<"-1"<<endl;
      }
      return 0;
  }
  ```

  

## 102. 特殊的正方形

- tips：这种上下对称的可以用char二维数组存 再分开输出。注意一下比较小的情况能不能正常输出。

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int n;
	char a[105][105];
	cin>>n;
	for(int i=1;i<=(n+1)/2;i++)
	{
		int w=1;
		for(int j=2;j<=i;j++)
		{
			if(j%2==0)
			a[i][w++]='+';
			else
			a[i][w++]='.';
		}
		for(int k=1;k<=(n-2*(i-1));k++)
		{
			if(i%2==0)
			a[i][w++]='.';
			else
			a[i][w++]='+';
		}
		for(int j=i;j>=2;j--)
		{
			if(j%2==0)
			a[i][w++]='+';
			else
			a[i][w++]='.';
		}
	}
	for(int i=1;i<=(n+1)/2;i++)
	{
		for(int j=1;j<=n;j++)
		cout<<a[i][j];
		cout<<endl;
	}
	if(n%2==0)
	{
		for(int i=1;i<=n;i++)
		cout<<a[n/2][i];
		cout<<endl;
	}
	for(int i=(n+1)/2-1;i>=1;i--)
	{
		for(int j=n;j>=1;j--)
		cout<<a[i][j];
		cout<<endl;
	}
	return 0;
 } 
```

[#386. 特殊的正方形](http://oj.daimayuan.top/problem/386)



## 103. 走楼梯2

- 还没完全学会的dp题、、注释见下

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  long long dp[55][3];
  //dp[i][j]表示的是上到第i级台阶，走了j次两级台阶 
  int main()
  {
  	int n;
  	cin>>n;
  
  	dp[0][0]=1;
  	for(int i=0;i<=n;i++)
  	{
  		for(int j=0;j<3;j++)
  		{
  			if(j!=2)
  			dp[i+2][j+1]+=dp[i][j];
  			dp[i+1][0]+=dp[i][j];
  		}
  	}
  	cout<<dp[n][0]+dp[n][1]+dp[n][2]<<endl;
  	return 0;
  }
  ```

[走楼梯2 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/course/11/problem/129)



## 103 走路 （回）（dp）

- 又是dp题...没看懂题解

  ```C++
  #include <bits/stdc++.h>
   
  #define int long long
  using namespace std;
  const int N = 1e5 + 107;
  bool f[103][N];
   
  signed main() {
      ios::sync_with_stdio(0);
      cin.tie(0);
      int n, m;
      cin >> n >> m;
      f[0][0] = true;
      for (int i = 1; i <= n; i++) {
          int a, b;
          cin >> a >> b;
          for (int j = m; j >= min(a,b); j--) {
              f[i][j] |= f[i - 1][j - min(a,b)];
              if (j >= max(a,b)) f[i][j] |= f[i - 1][j - max(a,b)];
          }
      }
   
      for (int i = 0; i <= m; i++) {
          if (f[n][i]) cout << "1";
          else cout << "0";
      }
   
   
  }
  ```

[走路 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/course/11/problem/460)



## 104 简单分数统计（map）

- tips: map容器的应用

```C++
#include<bits/stdc++.h>
using namespace std;
map<string,int> pro;
map<string,int> fri;
string peo[205];
int main()
{
	int n,m,k;
	cin>>n>>m>>k;
	for(int i=0;i<n;i++)
	cin>>peo[i];
	for(int i=0;i<m;i++)
	{
		string s;
		int x;
		cin>>s>>x;
		pro[s]=x;
	}
	for(int i=0;i<k;i++)
	{
		string s1,s2,s3;
		cin>>s1>>s2>>s3;
		if(s3=="AC")
		{
			fri[s1]+=pro[s2];
		}
	}
	for(int i=0;i<n;i++)
	cout<<peo[i]<<" "<<fri[peo[i]]<<endl;
	return 0;
}
```

[简单分数统计 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/problem/455)



## 105 Alice的德州扑克

- 简单的模拟判断，注意细节就行

```C++
#include<bits/stdc++.h>
using namespace std;
char ans[8][20]={"ROYAL FLUSH","STRAIGHT FLUSH", "FOUR OF A KIND","FULL HOUSE","FLUSH","STRAIGHT","FOLD"};
int flag[8],a[6],b[6];
int f=1,f2=0;
int main()
{
	for(int i=0;i<5;i++)
	cin>>a[i];
	for(int i=0;i<5;i++)
	cin>>b[i];
	
	for(int i=0;i<4;i++)
	{
		if(!(a[i]+1==a[i+1]&&b[i]==b[i+1]&&a[4]==14))
		flag[0]=1;
		if(!(a[i]+1==a[i+1]&&b[i]==b[i+1]))
		flag[1]=1;
		if(a[i]==a[i+1])
		{
			f2=max(f2,++f);
		}
		else
		f=1;
		if(b[i]!=b[i+1])
		flag[4]=1;
		if(a[i]+1!=a[i+1])
		flag[5]=1;
	}
	if(f2<4)
	flag[2]=1;
	if(!(f2==3&&(a[0]==a[1]||a[3]==a[4])))
	flag[3]=1;

	for(int i=0;i<6;i++)
	{
		if(!flag[i])
		{
			cout<<ans[i]<<endl;
			return 0;
		}
	}
	
	cout<<ans[6]<<endl;
	return 0;
}
```

[Alice的德州扑克 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/course/11/problem/453)



## 106 订单编号(并查集问题)（map）

- new！并查集问题！

  -  找根

    ```C++
    int findRoot(int x)
    {
    	if(mp[x]!=x)
    	mp[x]=findRoot(mp[x]);
    	return mp[x];
    }
    ```

  - 合并

    ```C++
    void unionTree(int a,int b)
    {
    	int x=findRoot(a);
    	int y=findRoot(b);//在执行findRoot的时候就已经把b的值变成它的根的值了 
    	if(x==y) return;
    	if(x>y) swap(x,y);//让大的还是让小的为根可以根据题目决定
    	mp[x]=y; 
    	return ;
    }
    ```

- 关于map

  - 查找时unordered_map会比较快，这题用map会超时
  - count(x): 找到返回1，找不到返回0。注意count找的是key值！
  - 另：find(x)中的x也是key值，找不到返回mp.end()。

- 本题思路：把出现过的数字并在一堆，根为这一堆里最大的数字+1（即重复出现这堆数字中的数时应该取的数字），用ans存最后的答案输出即可

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  int ans[N],a[N];
  int n;
  unordered_map<int,int> mp;
  int findRoot(int x)
  {
  	if(mp[x]!=x)
  	mp[x]=findRoot(mp[x]);
  	return mp[x];
  }
  void unionTree(int a,int b)
  {
  	int x=findRoot(a);
  	int y=findRoot(b);//在执行findRoot的时候就已经把b的值变成它的根的值了 
  	if(x==y) return;
  	if(x>y) swap(x,y);//这题让大的为根
  	mp[x]=y; 
  	return ;
  }
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		cin>>a[i];
  		mp[a[i]]=a[i]; 
  	}
  	for(int i=1;i<=n;i++)
  	{
  		int now=a[i];
  		int root=findRoot(a[i]);
  		
  		if(now==root) //如果这个数字还没被使用过 
  		{
  			ans[i]=now;
  			if(mp.count(now+1)==0) mp[now+1]=now+1;//如果比这个数字大一的数字还没有使用过 
  			unionTree(now,now+1);//并进去作为这个的根 
  		}
  		else //如果这个数字已经使用过
  		{
  			ans[i]= root;
  			if(mp.count(root+1)==0) mp[root+1]=root+1;
  			unionTree(root,root+1);
  		 } 
  	}
  	for(int i=1;i<=n;i++)
  	cout<<ans[i]<<" ";
  	
  	return 0;
  }
  ```

[订单编号 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/course/11/problem/465)

## 107 饿饿 饭饭

- 二分答案题

- 思路：二分法，check轮数x，找到最后一轮的情况，然后模拟一下队列

- 注意：

  - 注意数据规模，k能到10的14次方~~（谁家阿姨打饭能打这么多次啊）~~，要用long long
  - 如果是从1开始存~~（平时习惯从0开始存的，今天一时兴起从1存然后掉入坑里orz）~~，最后遍历的时候，如果刚好等于n，取余结果是会为0的...也就是a[n]被略过了，特判一下吧

- 最后一个测试点超时了，很崩溃，试着用map存了一次删除出队的数据，but失败了，##肝不动了我选择摆烂

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const long long N=1e5+10;
  long long n,k,ans=-2,sum=0;
  long long a[N];
  long long check(int x)
  {
  	long long s=0; //s表示的是x轮吃掉的饭的数量 
  	for(long long i=1;i<=n;i++)
  	{
  		if(a[i]<x)
  		s+=a[i];
  		else
  		s+=x;
  	}
  	if(k-s>=0) return (k-s); //返回阿姨还应该打的饭的数量 
  	else return -1;
  }
  int main()
  {
  	cin>>n>>k;
  	for(long long i=1;i<=n;i++)
  	{
  		cin>>a[i];
  		sum+=a[i];
  	}
  	
  	if(sum<k)
  	{
  		cout<<"-1"<<endl;
  		return 0;
  	}
  	else if (sum==k)
  	{
  		return 0;
  	 } 
  	else
  	{
  	long long l=0,r=k,t;
  	while(l<=r)
  	{
  		long long mid=l+(r-l)/2; //表示轮多少圈  
  		if(check(mid)!=-1) 
  		{
  			t=check(mid);
  			ans=mid;
  			l=mid+1;
  		}
  		else 
  		r=mid-1;
  	}
  	
  	for(long long i=1;i<=n;i++)
  	{
  		if(a[i]<=ans)
  		a[i]=0;
  		else
  		a[i]-=ans;
  	}
  	
  	long long i=1;
  	while(1)
  	{
  		if(t==0)
  		break;
  		if(a[i]!=0)
  		{
  			t--;
  			a[i]--;
  		}
  		i++;
  		if(i==n+1)
  		i=1;
  	}
  	for(long long j=0;j<n;j++)
  	{
  		if((i+j)%n==0&&a[n]!=0)
  		cout<<n<<" ";
  		if(a[(i+j)%n]!=0)
  		cout<<(i+j)%n<<" ";
  		
  	}
  	
  	}
  
  	return 0;
   } 
  ```

  [饿饿 饭饭 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/course/11/problem/463)

## 108 任务分配（回）（dp）

- 状态转移方程依然没看懂、、这周必把dp刷会

  ```c++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  int dp[1004];
  struct node {
  	int l,r,w;
  }ms[1004];
  bool cmp(node a,node b){
  	if(a.r == b.r) return a.l > b.l;
  	return a.r < b.r;
  }
  int main(){
   	int n; 
   	cin >> n;
      for(int i = 1; i <= n; i++){
  		cin >> ms[i].l >> ms[i].r >> ms[i].w;
  	}
  	sort(ms + 1,ms + 1 + n,cmp);
  	for(int i = 1; i <= n; i++){
  		for(int j = 1; j <= ms[i].l; j++){
  			dp[j] = max(dp[j],dp[j - 1]);
  			if(j == ms[i].l){
  				dp[ms[i].r] = max(dp[ms[i].l] + ms[i].w,dp[ms[i].r]);
  			}
  		}
  	}
  	int maxn = 0;
  	for(int j = 1; j <= 1000; j++){
  		maxn = max(dp[j],maxn);
  	}
  	cout << maxn << '\n';
  	return 0;
  }
  ```

  [任务分配 - 题目 - Daimayuan Online Judge](http://oj.daimayuan.top/problem/461)

  

















# 洛谷dp题

- 试图苦练dp然后刚刚入门、、

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

  

## P8697 [蓝桥杯 2019 国C] 最长子序列

- 感觉不太对劲，题目说用dp但是我很简单就过掉了（？

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  string s1,s2;
  int ans,len1,len2;
  int main()
  {
  	cin>>s1>>s2;
  	len1=s1.length();
  	len2=s2.length(); 
  	for(int i=0;i<len1;i++)
  	{
  		if(s1[i]==s2[ans])
  		{
  			ans++;
  		}
  	 } 
  	 cout<<ans<<endl;
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



## P1802 5倍经验日

- 01背包问题
- 注意：两个测试点没过
  - 最后结果很大应该开long long
  - 注意数据范围，药水数量x是可以为0的！！也有不用药水就能打败的敌人！

```C++
#include<bits/stdc++.h>
using namespace std;
int n,x;
long long lose[1010],win[1010],use[1010],dp[1010][1010];
int main()
{
	cin>>n>>x;
	for(int i=1;i<=n;i++)
	{
		cin>>lose[i]>>win[i]>>use[i];
	}
	
	for(int i=1;i<=n;i++)
	{
		for(int j=0;j<=x;j++)
		{
			if(j>=use[i])
			dp[i][j]=max(dp[i-1][j],dp[i-1][j-use[i]]+win[i]);
			dp[i][j]=max(dp[i][j],dp[i-1][j]+lose[i]);	
		}
	}
	
	cout<<5*dp[n][x]<<endl;
	return 0;
}
```



## P1434 [SHOI2002] 滑雪

-  dfs+记忆化搜索

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int r,c,ans;
  int mp[105][105],dp[105][105];//dp[i][j]表示从i j出发的最长道路是多少 
  int xx[5]={0,1,0,-1};
  int yy[5]={-1,0,1,0};
  int dfs(int x,int y)
  {
  	if(dp[x][y]) return dp[x][y];//记忆化搜索
  	dp[x][y]=1; 
  	for(int i=0;i<4;i++)
  	{
  		int nx=x+xx[i];
  		int ny=y+yy[i];
  		if(nx>=1&&nx<=r&&ny>=1&&ny<=c&&mp[x][y]>mp[nx][ny])
  		{
  			dfs(nx,ny);
  			dp[x][y]=max(dp[x][y],dp[nx][ny]+1);
  		}
  	}
  	
  	return dp[x][y];
  	
   } 
  int main()
  {
  	cin>>r>>c;
  	
  	for(int i=1;i<=r;i++)
  	{
  		for(int j=1;j<=c;j++)
  		{
  			cin>>mp[i][j];
  		}
  	}
  	
  	for(int i=1;i<=r;i++)
  	{
  		for(int j=1;j<=c;j++)
  		{
  			ans=max(ans,dfs(i,j));
  		}
  	}
  	
  	cout<<ans<<endl;
  	return 0;
  }
  ```

  





