# Week 14

[TOC]

# Div2 每日一题

## 202 路径计数（dp)

- dp，熟悉的dp入门！！

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e9+7;
  long long mp[110][110],dp[110][110];
  int n;
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=n;j++)
  		{
  			cin>>mp[i][j];
  		}
  	}
  	for(int i=1;i<=n;i++)
  	{
  		if(mp[1][i]==0)
  		break;
  		dp[1][i]=1;
  	}
  	for(int i=1;i<=n;i++)
  	{
  		if(mp[i][1]==0)
  		break;
  		dp[i][1]=1;
  	}
  	for(int i=2;i<=n;i++)
  	{
  		for(int j=2;j<=n;j++)
  		{
  			if(mp[i][j]!=0)
  			dp[i][j]=(dp[i-1][j]+dp[i][j-1])%N;
  		}
  	}
  	
  	cout<<dp[n][n]<<endl;
  	return 0;
   } 
  ```

  

## 203 最大和上升子序列（线性dp）

- 跟最长上升子序列差不多，注意初始化和状态转移方程改改就行

```C++
#include<bits/stdc++.h>
using namespace std;
int n,ans;
int a[1005],dp[1005]; //dp[i]表示以a[i]结尾的最大和上升子序列的和 
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		cin>>a[i];
		dp[i]=a[i];
	}
	
	for(int i=2;i<=n;i++)
	{
		for(int j=1;j<i;j++)
		{
			if(a[i]>a[j])
			dp[i]=max(dp[i],dp[j]+a[i]);
		}
		ans=max(ans,dp[i]);
	}
	
	cout<<ans<<endl;
	
	return 0;
}
```



## 204 加一（dp)(回)

- 因为最后要%mod，所以只需要考虑后10位。然后我们在后10位里找规律，得状态转移方程

  ```C++
  #include<bits/stdc++.h>
  
  #define ll long long
  using namespace std;
  
  const int mod = 1e9 + 7;
  
  int dp[13][200004];
  
  void solve(){
      for(int i = 0; i <= 9; i++){
  		for(int j = 0; j <= 10; j++) {
  			if(i + j < 10) dp[i][j] = 1;
  			else dp[i][j] = 2;
  		}
  	}
  
      for(int i = 0; i <= 9; i++){
  		for(int k = 10; k <= 200000; k++){
  			dp[i][k] = dp[i][k - 9] + dp[i][k - 10];
  			dp[i][k] %= mod;
  		}
  	}
      
      int t; cin >> t;
      while(t--){
  		int n,m;
  		cin >> n >> m;
  		int x = n;
  		ll ans = 0;
  		while(x){
  			ans += dp[x % 10][m];
  			ans %= mod;
  			x /= 10;
  		}
  		cout << ans << '\n';
  	}
  }
  
  int main(){
      solve();
      return 0;
  }
  
  ```

  

## 205 跳跳

- 这个数据量可以直接暴力！
- 魔法阵取差值的最简式（比例不变，两边值最小

```C++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<ll, ll>PII; 
const int MOD = 1e9 + 7;
const int N = 200010;
ll f[N+50][10];

int gcd(int x, int y)
{
	while (y ^= x ^= y ^= x %= y);
	return x;
}
int main()
{
	ll n, x, y;
	vector<PII>v;
	cin >> n;
	map<PII, int>mymap;
	for (int i = 0; i < n; i++)
	{
		cin >> x >> y;
		v.push_back({ x,y });
	}
	ll res = 0;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			if (j == i)continue;
			int x = v[j].first - v[i].first;
			int y = v[j].second - v[i].second;
			if (x == 0 || y == 0)
			{
				x = x != 0 ? x / abs(x) : x;
				y = y != 0 ? y / abs(y) : y;
			}
			else
			{
				int num = abs(gcd(x, y));
				x /= num;
				y /= num;
			}
			if (mymap[{x, y}] == 0)
			{
				mymap[{x, y}] = 1;
				res++;
			}
		}
	}
	cout << res << endl;
	return 0;
}
```



## 206 异或和或

- 

```C++
#include<bits/stdc++.h>
using namespace std;
int main()
{
    int t;
    cin >> t;
    while (t--)
    {
        string s1, s2;
        cin >> s1 >> s2;
        int n = s1.length(), m = s2.length();
        if (n != m)
        {
            cout << "NO" << endl;
            continue;
        }
        int s1_zero = 0, s1_one = 0, s2_zero = 0, s2_one = 0;
        for (int i = 0; i < n; i++)
        {
            if (s1[i] == '0')s1_zero++;
            else s1_one++;

            if (s2[i] == '0')s2_zero++;
            else s2_one++;
        }
        if (s2_one == 0 && s1_one != 0)cout << "NO" << endl;
        else if (s2_one != 0 && s1_one == 0)cout << "NO" << endl;
        else cout << "YES" << endl;
    }

    return 0;
}
```



## 207 01序列（前缀和）（map哈希表）

- 用前缀和存前面出现了几个1

  刚开始用暴力写法没过

  ```C++
   for(int i=1;i<=len;i++)
  	 {
  	 	if(a[i]==k)
  	 	ans++;
  	 	for(int j=1;j<=i;j++)
  	 	{
  	 		if(a[i]-a[j]==k)
  	 		ans++;
  		 }
  	 }
  ```

- 前缀和，然后用map存相同前缀和的数量

  正常情况下，res+=前缀和为i的数量*前缀和为i-k的数量

  当k==0时，特判，子串数量为连续ans个0的子串

```C++
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+10;
long long k,len,res;
long long a[N];
string s;
map<long long,long long> mp;
int main()
{
	cin>>k;
	cin>>s;
	len=s.length();
	mp[0]=1;
	if(k==0)
	{
		s+='1';
		len++;
		long long ans=0;
		for(int i=0;i<len;i++)
		{
			if(s[i]=='0')
			ans++;
			else
			{
				res+=ans*(ans+1)/2;
				ans=0;
			}
		}
	}
	else
	{
		for(int i=1;i<=len;i++)
		{
			a[i]=a[i-1]+(int)(s[i-1]-'0');
			mp[a[i]]++;
		}
		long long ans=0;
		while(mp[ans+k]!=0)
		{
			res+=mp[ans]*mp[ans+k];
			ans++;
		}
	}
	cout<<res<<endl;
	return 0;
}
```



## 301 出栈序列判断

- 注意！！！！非空判断只能放在前面！！！要先判断非空，不然如果s已经空了还取s.top()就会有问题！！！
- 注意！！！！'\n'比endl快！！！！这题如果用endl就会超时！！！'\n'不会！！！！！！

```C++
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+10;
int a[N];
int n;
int main()
{
	cin>>n;
	for(int i=0;i<n;i++)
	{
		cin>>a[i];
	}
	stack<int>s;
	int flag=0;
	for(int i=1;i<=n;i++)
	{
		s.push(i);
		cout<<"push "<<i<<'\n';

		while(!s.empty()&&s.top()==a[flag])
		{
			cout<<"pop"<<'\n';
			s.pop();
			flag++;
		}

	}
	while(!s.empty())
	{
		cout<<"pop"<<'\n';
		s.pop();
	}
	return 0;
}
```



## 302 序列维护

- 直接用vector

```C++
#include<bits/stdc++.h>
using namespace std;
vector<int>v;
int main()
{
	int m;
	cin>>m;
	while(m--)
	{
		string s;
		cin>>s;
		if(s=="insert")
		{
			int x,y;
			cin>>x>>y;
			v.insert(v.begin()+x,y);
		}
		else if(s=="delete")
		{
			int x;
			cin>>x;
			v.erase(v.begin()+x-1);
		}
		else if(s=="query")
		{
			int x;
			cin>>x;
			cout<<v[x-1]<<endl;
		}
	
	}
	return 0;
}
```



## 303 网格判断

-  基础题捏，注意细节不要着急

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int pd1,pd2,pd3;
  char a[26][26];
  bool flag=true;
  int main()
  {
  	int n;
  	cin>>n;
  	for(int i=0;i<n;i++)
  	{
  		for(int j=0;j<n;j++)
  		{
  			cin>>a[i][j];
  			if(pd2==3||pd3==3)
  			flag=false;
  			if(flag)
  			{
  				if(a[i][j]=='W')
  				{
  					pd1++;
  					pd2++;
  					pd3=0;
  				}
  				else
  				{
  					pd2=0;
  					pd3++;
  				}
  			}
  			
  		}
  		if(pd1!=n/2)
  		flag=false;
  		pd1=0;
  		pd2=0;
  		pd3=0;
  	}
  	if(flag)
  	for(int i=0;i<n;i++)
  	{
  		for(int j=0;j<n;j++)
  		{
  			if(pd2==3||pd3==3)
  			flag=false;
  			if(flag)
  			{
  				if(a[j][i]=='W')
  				{
  					pd1++;
  					pd2++;
  					pd3=0;
  				}
  				else
  				{
  					pd2=0;
  					pd3++;
  				}
  			}
  		}
  		if(pd1!=n/2)
  		flag=false;	
  		pd1=0;
  		pd2=0;
  		pd3=0;
  	}
  	cout<<flag<<endl;
  	return 0;
  }
  ```

  

## 304 整齐的数组

- __gcd()函数

```C++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	cin.tie(0);
	int t;
	cin>>t;
	while(t--)
	{
		int n;
		long long ans=0;
		set<long long>a;
		cin>>n;
		for(int i=0;i<n;i++)
		{
			long long x;
			cin>>x;
			a.insert(x);
		}
		bool f=0,f2=0;
		long long pre;
		for(auto it:a)
		{
			if(!f){
				pre=it;
				f=1;
			}
			else if(!f2)
			{
				ans=it-pre;
				f2=1;
			}
			else{
				ans=__gcd(it-pre,ans);
			}
		}
		if(ans)
		cout<<ans<<'\n';
		else
		cout<<"-1"<<'\n';
	}
	return 0;
 } 
```













# 洛谷dp题

## P1020 [NOIP1999 普及组] 导弹拦截

- 最长不升（降）子序列

- 狄尔沃斯定理（Dilworth）：对于任意有限偏序集，其中最长链的数目必定等于其最小反链划分中反链的数目。

  在本题中，第二问即求最长上升子序列的长度

- 时间复杂度为O(n2)的算法

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int n,ans;
  int a[1005],dp[1005]; //dp[i]表示以a[i]结尾的最大和上升子序列的和
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		cin>>a[i];
  		dp[i]=a[i];
  	}
  	
  	for(int i=2;i<=n;i++)
  	{
  		for(int j=1;j<i;j++)
  		{
  			if(a[i]>a[j])
  			dp[i]=max(dp[i],dp[j]+a[i]);
  		}
  		ans=max(ans,dp[i]);
  	}
  	
  	cout<<ans<<endl;
  	
  	return 0;
  }
  ```

- 二分优化(+贪心思想)：low[i]表示的是子序列长度为i的情况下，该序列最后一个数的最小值(该值越小后面能接的数就越多)

  ```C++
  eg. 1 4 7 2 5 9
  low[1]=1;
  low[2]=2;//此时序列为1 2
  low[3]=5;
  ....
  ```

  所以只需要遍历一次a[n]数组，二分法找出low[n]中第一个大于a[i]的数，并把它换成a[i]；

  如果都比a[i]小，最长子序列长度就能+1，然后把a[i]接在后面

  注意：low[n]的结果并不是最长子序列的排列

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=5e4+10;
  int a[N],low[N];
  int n,js=1;
  int binary_search(int x)
  {
  	int l=1,r=js;
  	while(l<=r)
  	{
  		int mid=l+(r-l)/2;
  		if(x<low[mid]) r=mid-1;
  		else l=mid+1;
  	}
  	return l;
  }
  int main()
  {
  	while(cin>>a[++n])
  	{
  		if(cin.get()=='\n')
  		break;
  	}
  	low[1]=a[1];
  	for(int i=2;i<=n;i++)
  	{
  		if(a[i]>low[js])
  		low[++js]=a[i];
  		else
  		{
  			int k=binary_search(a[i]);
  			low[k]=a[i];
  		}
  	}
  	cout<<js<<endl;
  	return 0;
  }
  ```

  



## P1439 【模板】最长公共子序列(线性dp)（二分）

- 线性dp，时间复杂度为O(n2)，二维数组开不了1e5

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e3+10;
  int n,maxn;
  int a[N],b[N],dp[N][N];//dp[i][j]表示以a[i]结尾和以b[j]结尾的最长公共子序列长度 
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	cin>>a[i];
  	for(int i=1;i<=n;i++)
  	cin>>b[i];
  	
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=n;j++)
  		{
  			if(b[j]==a[i])
  			dp[i][j]=max(dp[i-1][j-1]+1,dp[i][j]);
  			else 
  			dp[i][j]=max(dp[i][j-1],dp[i-1][j]);
  		}
  	}
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=n;j++)
  		maxn=max(maxn,dp[i][j]);
  	}
  	cout<<maxn<<endl;
  	return 0;
  }
  ```

- 二分法，时间复杂度nlogn

  - 注意读题：每行都是1~n的排列，也就是说第一排有的数第二排都有
  - 思路：把a[N]从1~n**升序**赋值，记录每个数字变成了哪个值，再将b[N]的值变成对应的，这样就将该题变成了在b[N]中查找最长升序子序列的问题！
  - 开longlong

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e5+10;
  int n,a[N],b[N],belong[N],low[N],ans=1;
  int binary_search(int x)
  {
  	int l=1,r=ans;
  	while(l<=r)
  	{
  		int mid=l+(r-l)/2;
  		if(x<low[mid]) r=mid-1;
  		else l=mid+1; 
  	}
  	return l;
  }
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		cin>>a[i];
  		belong[a[i]]=i;
  	}
  	
  	for(int i=1;i<=n;i++)
  	{
  		cin>>b[i];
  		b[i]=belong[b[i]];
  	}
  	
  	low[ans]=b[1];
  	for(int i=2;i<=n;i++)
  	{
  		if(b[i]>low[ans])
  		low[++ans]=b[i];
  		else
  		low[binary_search(b[i])]=b[i];
  	}
  	
  	cout<<ans<<endl;
  	
  	return 0;
  }
  ```


## P2758 编辑距离

- 思路：dp[i][j]表示a的前i个需要通过几次操作得到b的前j个 

  ```C++
  dp[i][j]=dp[i-1][j]+1;           //删除
  dp[i][j]=dp[i][j-1]+1;           //插入(注意)
  dp[i][j]=dp[i-1][j-1]+1;         //修改
  dp[i][j]=dp[i-1][j-1];           //不变
  ```

  AC代码

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=2010;
  string a,b;
  int len1,len2;
  int dp[N][N];//dp[i][j]表示a的前i个需要通过几次操作得到b的前j个 
  int main()
  {
  	cin>>a>>b;
  	len1=a.length();
  	len2=b.length();
  	
  //初始化 因为状态转移方程中有i-1,j-1，如果数组从0开始存会出现段错误 
  	for(int i=len1;i>=1;i--) a[i]=a[i-1];
  	for(int i=len2;i>=1;i--) b[i]=b[i-1]; 
  	
  //边界 
  	for(int i=0;i<=len1;i++) dp[i][0]=i; 
  	for(int i=0;i<=len2;i++) dp[0][i]=i;
  	
  	for(int i=1;i<=len1;i++)
  	{
  		for(int j=1;j<=len2;j++)
  		{
  			int x=1;
  			if(a[i]==b[j]) x=0;
  			dp[i][j]=min(min(dp[i-1][j]+1,dp[i][j-1]+1),dp[i-1][j-1]+x);
  		}
  	}
  	
  	cout<<dp[len1][len2]<<endl;
  	return 0;
   } 
  ```

  





# 第十三届蓝桥杯

## D 选数异或(dp)

- 求存在性问题可以考虑用dp

```C++
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+10;
int n,m,x;
int dp[N];//dp[i]表示以i为右端点的最大左端点位置 
map<int,int>mp;
int main()
{
	cin.tie(0);
	cout.tie(0);//加速用
	cin>>n>>m>>x;
	for(int i=1;i<=n;i++)
	{
		int a;
		cin>>a;
		dp[i]=max(mp[a^x],dp[i-1]);
		mp[a]=i; 
	}
	
	while(m--)
	{
		int l,r;
		cin>>l>>r;
		if(dp[r]>=l) cout<<"yes"<<'\n';
		else cout<<"no"<<'\n';
	}
	return 0;
}
```





























