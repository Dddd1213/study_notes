# Week 17

[TOC]

## 代码源每日一题

### 605 优美！最长上升子序列（线性dp）

- 一开始没看懂题，要求是**下标**满足前是后的约数

- 跟最长上升子序列差不多，把内层遍历稍微改改就行

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  int a[N],dp[N];
  int mx;
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	int t;
  	cin>>t;
  	while(t--)
  	{
  		int n;
          mx=1;//这里记得初始化一下mx，不然如果是全降没有进入if就会保留上一轮的结果
  		cin>>n;
  		for(int i=1;i<=n;i++)
  		{
  			cin>>a[i];
  			dp[i]=1;
  		}
  		
  		for(int i=1;i<=n;i++)
  		{
  			for(int j=i*2;j<=n;j+=i)
  			{
  				if(a[i]<a[j])
  				{
  					dp[j]=max(dp[i]+1,dp[j]);
  					mx=max(mx,dp[j]);
  				}
  				
  			}
  //			for(int k=1;k<=n;k++)
  //			cout<<dp[k]<<" ";
  //			cout<<endl;
  		}
  		cout<<mx<<'\n'; 
  	}
  	
  	return 0;
   } 
  ```

  

### 606 巨大的牛棚（二维dp）

- 很经典的二维dp，通常这种情况下先找个小点的例子画一下每个点的值，然后再推动态转移方程会比较容易

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int n,t,mx=-1;
  int mapp[1010][1010],dp[1010][1010]; //表示坐标为i j的点的最大正方形边长 
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n>>t;
  	memset(mapp,1,sizeof(mapp));
  	for(int i=1;i<=t;i++) 
  	{
  		int x,y;
  		cin>>x>>y;
  		mapp[x][y]=0;
  	}
  
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=n;j++)
  		{
  			if(mapp[i][j])
  			dp[i][j]=min(dp[i-1][j],min(dp[i][j-1],dp[i-1][j-1]))+1;
  			mx=max(mx,dp[i][j]);
  		}
  	}
  	cout<<mx;
  	return 0;
   }
  ```

### 607 高利贷（算利率）（二分法）

- 已经写过一次了但还是看不懂题嗯嗯

- 100/(1+p)+100/pow(1+p,2)+...+100/pow(1+p,k) ≈ 1000

- 注意写这种是零点几小数的二分法不能用l=mid+1啥啥，就退循环了；

  精度要求使用在while(r-l>1e-6)

  ```C++
  #include <iostream>
  #include <iomanip>
  #include <cmath>
  using namespace std;
  const int max_n = 1e6;
  const int max_m = 1e6;
  const int max_k = 300;
  const int max_p = 5;
  
  int n, m, k;
  
  bool judge(double p) {
  	double down = 1 + p;
  	double sum = 0.0;
  	for (int i = 0; i < k; i++) {
  		sum += m / down;
  		down *= 1 + p;
  	}
  	return sum - n > 0;
  }
  
  double bin_search() {
  	double l = 0.0, r = max_p, m;
  	while (r - l > 1e-6) {
  		m = (l + r) / 2;
  		if (judge(m)) l = m;
  		else r = m;
  	}
  	return l;
  }
  
  int main() {
  	cin >> n >> m >> k;
  	cout << setiosflags(ios::fixed) << setprecision(6) << bin_search() << endl;
  	return 0;
  }
  ```

### 701 背包

- 直接遍历判断就行，如果这个物品少于w/2就直接装进去，如果大于就判断该物品是否满足条件

```C++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
const int N=2e5+10;
ll vol[N];
int main()
{
	ll n,w,t;
	cin>>t;
	while(t--)
	{
		bool f=0;
	ll ans=0,res=0;
	cin>>n>>w;
	
	for(int i=1;i<=n;i++)
	cin>>vol[i];
	
	for(int i=1;i<=n;i++)
	{
		if(vol[i]<=(w+1)/2)
		{
			res+=vol[i];
		}
		else if(vol[i]>=(w+1)/2&&vol[i]<=w)
		{
			f=1;
		}
		if(res>=(w+1)/2&&res<=w)
		f=1;
	}
	if(f)
	cout<<"YES\n";
	else
	cout<<"NO\n";
	}
	return 0;
}
```

### 702 三回文序列（二维前缀和）

```C++
#include <iostream>
using namespace std;
const int max_num = 26;
const int max_len = 2e5;

int sum[max_num + 1][max_len + 1], indices[max_num + 1][max_len + 1];

int main() {
	int t, n, x;
	cin >> t;
	for (int i = 0; i < t; i++) {
		cin >> n;
		for (int j = 1; j <= n; j++) {
			cin >> x;
			for (int k = 1; k <= max_num; k++) {
				sum[k][j] = sum[k][j - 1];
			}
			sum[x][j]++;
			indices[x][sum[x][j]] = j;
		}

		int ans = 0;
		for (int a = 1; a <= 26; a++) {
			for (int k1 = 0; k1 <= sum[a][n]; k1++) {
				int l, r;
				if (k1) {
					l = indices[a][k1];
					r = indices[a][sum[a][n] - k1 + 1];
				}
				else {
					l = 0;
					r = n + 1;
				}

				if (l > r) break;
				else if (l == r) ans = max(ans, 2 * k1 - 1);
				else {
					for (int b = 1; b <= 26; b++) {
						int k2 = sum[b][r - 1] - sum[b][l];
						ans = max(ans, k1 * 2 + k2);
					}
				}
			}
		}
		cout << ans << endl;
	}
	return 0;
}
```

### 703 简单的异或问题

- 思路：0~pow(2,m-1) 的所有数进行异或后，得到的结果为0

  - 证明：例如m=3时，先按 0 7， 1 6，2 5，3 4 分组进行异或，得到的结果都是7，然后四个1111（7的二进制表示）进行异或，得到0

  想要得到数a，就只需要pow(2, m-1)和pow(2, m-1-a)进行异或运算，题目要求最多能选多少数，就可以把前面都选上（最后异或会变0）；

  特判：m=1时，想得0只能选0，想得1可以两个都选

```C++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
signed main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	
	ll n, m;
    cin >> n >> m;
    ll res = 1;
    res <<= m;
    if (m == 1 && n == 0)
    {
    cout << 1 << endl;
    }
    else if (m == 1&&n==1)
    {
    cout << 2 << endl;
    }
 	else 
 	cout << (1LL)*(res - min(n,1LL)) << endl;
	return 0;
}
```





### 704  子序列的循环挪动

- substr(a, a) 截取从a开始往后b位

- 思路：每次都把需要变的部分截出来，变好再放回去，更新s即可

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  signed main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	string s;
  	cin>>s;
  	int len=s.length();
  	int m;
  	cin>>m;
  	while(m--)
  	{
  		string ss;
  		int l,r,k;
  		cin>>l>>r>>k;
  		k=k%(r-l+1);
  		ss+=s.substr(0,l-1);
  		string s2=s.substr(l-1,r-l+1);
  		s2+=s2;
  //		cout<<"s2= "<<s2<<endl;
  		ss+=s2.substr(r-l-k+1,r-l+1);
  //		cout<<"k="<<k<<" l= "<<l<<" r= "<<r<<endl;
  //		cout<<"ss= "<<ss<<endl;
  		ss+=s.substr(r,len-r+1);
  		s=ss;
  	}
  	cout<<s;
  	return 0;
  }
  ```

  

### 705 弗拉德和糖果 II（插空）

- 插空

- 思路：先找出数量最多的糖果maxx，它们需要被分隔开，就要每个空之间都插入一个其他类型的糖果，所以其他糖果的总数sum应该至少为maxx-1

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  const int N=5e6+10;
  ll n,sum,maxx;
  signed main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	for(int i=0;i<n;i++)
  	{
  		ll x;
  		cin>>x;
  		sum+=x;
  		maxx=max(x,maxx);
  	}
  	
  	sum-=maxx;
  	
  	if(sum>=maxx-1)
  	cout<<"YES";
  	else
  	cout<<"NO";
  	
  	return 0;
  }
  ```



### 706 上帝的集合（差分）（优先队列）

- 解1：直接用vector暴力写的->超时；超int

  n能到1e6，如果每次操作2和3都需要全部遍历一遍，时间复杂度肯定会超；

  x最大1e12，用long long

  ```C++
  #include<bits/stdc++.h>
  #define int long long
  using namespace std;
  signed main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	int n;
  	cin>>n;
  	vector<int> st(n+10);
  	while(n--)
  	{
  		int op;
  		cin>>op;
  		if(op==1)
  		{
  			int x;
  			cin>>x;
  			st.push_back(x);
  		}
  		if(op==2)
  		{
  			int x;
  			cin>>x;
  			for(int i=0;i<st.size();i++)
  			{
  				st[i]+=x;
  			}
  		}
  		if(op==3)
  		{
  			sort(st.begin(),st.end());
  			cout<<*st.begin()<<'\n';
  			st.erase(st.begin());
  		}
  	}
  	return 0;
  }
  ```

- 优化：

  - 操作2，每个数加5，可以用**差分**，每使用一次操作2就用add记录一次，最后输出的时候直接加add，这样就不用全部遍历了

    新问题：后面加进来的数可能会多加

    解决：把后面输入的数先减一个add，再正常输出

  - 操作3：因为改变大小每次对每个数都会操作，并不会改变数字的**相对**大小，所以能直接放进**优先队列**排序，用小顶堆

    ```C++
    priority_queue<int> q; //默认是降序排列
    priority_queue<int,vector<int>,greater<int>> q;//升序
    struct cmp{
        bool operator()(const int a,const int b)
        {
            return a>b;
    }
    }
    priority_queue<int,vector<int>,cmp>//自定义
    ```

  - AC代码：

    ```C++
    #include<bits/stdc++.h>
    #define ll long long
    using namespace std;
    signed main()
    {
    	ios::sync_with_stdio(0);
    	cin.tie(0);
    	cout.tie(0);
    	int n;
    	cin>>n;
    	priority_queue<ll,vector<ll>,greater<ll>>que;
    	ll add=0;
    	while(n--)
    	{
    		int op;
    		cin>>op;
    		if(op==1)
    		{
    			ll x;
    			cin>>x;
    			que.push(x-add);
    		}
    		if(op==2)
    		{
    			ll x;
    			cin>>x;
    			add+=x;
    		}
    		if(op==3)
    		{
    			cout<<que.top()+add<<'\n';
    			que.pop();
    		}
    	}
    	return 0;
    }
    ```

### 707 最长公共子序列

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
