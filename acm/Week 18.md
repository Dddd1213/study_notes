# Week 18

[TOC]

## 801 喵喵序列（线段树）

- 没太看懂、、这周刷一下线段树专题再来、、

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  
  #define endl '\n';
  typedef long long ll;
  typedef pair<int, int> PII;
  const int N = 300050;
  unordered_map<ll, ll>mymap;
  
  void revise(ll k, ll l, ll r, ll x, vector<ll>& f, ll v)
  {
      if (l == r)
      {
          f[k] += v;
          return;
      }
      int m = (l + r) / 2;
      if (x <= m)revise(k + k, l, m, x, f, v);
      else
          revise(k + k + 1, m + 1, r, x, f, v);
      f[k] = (f[k + k] + f[k + k + 1]);
  }
  
  
  ll calc(ll k, ll l, ll r, ll x, ll y, vector<ll>& f)
  {
      if (l == x && y == r)
      {
          return f[k];
      }
      int m = (l + r) / 2;
      if (y <= m)return calc(k + k, l, m, x, y, f);
      else
          if (x > m)return calc(k + k + 1, m + 1, r, x, y, f);
          else
          {
              return (calc(k + k, l, m, x, m, f) + calc(k + k + 1, m + 1, r, m + 1, y, f));
          }
  }
  
  int main()
  {
      ll n, ans = 1, res = 0;
      cin >> n;
      vector<ll>a(n + 1), b, f1(4 * n), f2(4 * n);
      for (int i = 1; i <= n; i++)cin >> a[i];
      b = a;
      sort(b.begin(), b.end());
      mymap[b[1]] = ans++;
      //离散化操作
      for (int i = 2; i <= n; i++)
      {
          if (b[i] != b[i - 1])mymap[b[i]] = ans++;
      }
      for (int i = 1; i <= n; i++)
      {
          if (mymap[a[i]] != 1)res += calc(1, 1, n, 1, mymap[a[i]] - 1, f2);
          if (mymap[a[i]] != 1) 
          {
              ll ans = calc(1, 1, n, 1, mymap[a[i]] - 1, f1);
              revise(1, 1, n, mymap[a[i]], f2, ans);
          }
          revise(1, 1, n, mymap[a[i]], f1, 1);
      }
      cout << res << endl;
      return 0;
  }
  
  ```



## 802 漂亮数

- 题意：从X中取数a[i]得到长度为k的数b，b循环组成Y（也就是说Y由长度为k的轮回组成）

- 思路：先从x中取最高前k位数，如果x[i]大于y[i]，将y的最小位+1（记得进位），这样得到的y一定比x大

- 参考：[代码源：743、漂亮数 - 掘金 (juejin.cn)](https://juejin.cn/post/7087541096187691039)

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int main() {
      int n, k;
      cin >> n >> k;
      vector<int>x(n), y(k);
      string s;
      cin >> s;
      bool flag = true;
      for (int i = 0; i < n; i++)
      {
          x[i] = s[i] - '0';
          if (i < k)y[i] = x[i];
      }
      flag = false;
      for (int i = 0; i < n; i++)
      {
          for (int j = 0; j < k; j++)
          {
              if (y[j] < x[i + j])
              {
                  int ans = k - 1;
                  while (y[ans] == 9)ans--;
                  y[ans]++;
                  for (int l = ans + 1; l < k; l++)y[l] = 0;
                  flag = true;
                  break;
              }
              else if (y[j] > x[i + j])
              {
                  flag = true;
                  break;
              }
          }
          i += k - 1;
          if (flag)break;
      }
      cout << n << endl;
      for (int i = 0; i < n; i++)
      {
          cout << y[i % k];
      }
      return 0;
  }
  
  ```

  

## 803 真假字符串

- 思路：s1 s2在同一个位置有一个字符不同时，只需要把该位置的删除；位置不同时，判断下一个是否相同，如果s1[i]==s2[j+1]，则在s1中插入不同的字符。

```C++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	 string s,s1,s2;
    cin>>s1>>s2;
    if(s1==s2)
    {
        cout<<"1";
    }
    else
    {
        int u=0,d=0,js1=0,js2=0;
        while(js2<=1&&js1<=1&&(u<s1.length()||d<s2.length()))
        {
            if(s1[u]!=s2[d])
            {
                if(s1[u]==s2[d+1])
                {
                    string tp;
                    tp+=s2[d];
                    s1.insert(u,tp);
                    js1++;
                }
                else if(s2[d]==s1[u+1])
                { 
					string tp;
                    tp+=s1[u];
                    s2.insert(d,tp);
                    js2++;
                }
                else
                {
                    s1.erase(u,1);
                    s2.erase(d,1);
                    js1++;
                    js2++;
                }
            }
            u++,d++;
        }
        if(s1==s2&&js1==1&&js2==1)
        {
            cout<<"1";
        }
        else
        {
            cout<<"0";
        }
    }
	return 0;
 } 
```



## 804 走不出的迷宫

- 刚开始用了一种诡异的dfs然后绕不明白最后还是用了dp

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int h,w,maxn;
  int dp[110][110];
  //int xx[3]={0,1};
  //int yy[3]={1,0};
  char mp[110][110];
  //void dfs(int x,int y)
  //{
  //	for(int i=0;i<2;i++)
  //	{
  //		int nx=x+xx[i];
  //		int ny=y+yy[i];
  //		if(nx<=w&&ny<=h&&mp[nx][ny]=='.')
  //		{
  //			ans++;
  //			dfs(nx,ny);
  //			break;
  //		}
  //	}
  //}
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>h>>w;
  	for(int i=1;i<=h;i++)
  	{
  		for(int j=1;j<=w;j++)
  		{
  			cin>>mp[i][j];
  		}
  	}
  //	dfs(1,1);
  	
  	dp[1][1]=1;
  	maxn=1;
  	for(int i=1;i<=h;i++)
  	{
  		for(int j=1;j<=w;j++)
  		{
  			if(mp[i][j]=='.'&&((dp[i-1][j]!=0&&i-1>0)||(dp[i][j-1]!=0&&j-1>0)))
  			{
  				dp[i][j]=max(dp[i-1][j],dp[i][j-1])+1;
  				maxn=max(dp[i][j],maxn);
  			}
  		}
  	}
  	
  //	for(int i=1;i<=h;i++)
  //	{
  //		for(int j=1;j<=w;j++)
  //		{
  //			cout<<dp[i][j];
  //		}
  //		cout<<endl;
  //	}
  	
  	cout<<maxn<<'\n';
  	
  	return 0;
  }
  ```




## 805 最长同于子数组

- 同余：数之间差值能被一个数整除

- 思路：滑动窗口跑一下差值数组，计算最大公约数（>=2）。注意：当相邻的值相等时也算同余，但是gcd不大于1，所以要再计算一下相等的元素。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N = 100010;
  ll a[N], f[4 * N];
  
  ll gcd(ll a, ll b)
  {
      return b == 0 ? a : gcd(b, a % b);
  }
  
  void buildtree(ll k, ll l, ll r)
  {
      if (l == r)
      {
          f[k] = a[l];
          return;
      }
      int m = (l + r) / 2;
      buildtree(k + k, l, m);
      buildtree(k + k + 1, m + 1, r);
      f[k] = gcd(f[k + k], f[k + k + 1]);
  }
  
  ll calc(ll k, ll l, ll r, ll x, ll y)
  {
      if (l == x&&r==y)return f[k];
  
      int m = (l + r) / 2;
      if (y <= m)return calc(k + k, l, m, x, y);
      else
          if (x > m)return calc(k + k + 1, m + 1, r, x, y);
          else
          {
              return gcd(calc(k + k, l, m, x, m), calc(k + k + 1, m + 1, r, m + 1, y));
          }
  }
  
  int main()
  {
      cin.sync_with_stdio(false);
      cin.tie(0);
      cout.tie(0);
      int n, t;
      cin >> n;
      vector<ll>v(n + 1);
      for (int i = 1; i <= n; i++)cin >> v[i];
      for (int i = 1; i <= n; i++)
      {
          a[i] = abs(v[i] - v[i - 1]);
      }
      
      buildtree(1, 1, n);
      ll l = 1, r = 1, len = 1, ans = 1;
      while (r <= n)
      {
          ll num = calc(1, 1, n, l, r);
          if (num > 1)r++;
          else l++;
          while (l > r)r++;
          ans = l;
          while (num>1&&ans <= n && ans > 1 && v[ans] % num == v[ans - 1] % num)
          {
              ans--;
          }
          if (r - ans > len)
          {
              len = r - ans;
          }
      }
      
      cout << len;
      return 0;
  }
  ```

  

## 806 互质（欧拉筛）

- 思路：先找出序列A中的所有质因数，然后再在1~m里找

  找质数：欧拉筛

  ```c++
  int primes[N];//记录0~N的素数有哪些
  int st[N];//记录该数字有没有被筛掉，0表示未筛掉，1表示已筛掉
  int cnt;//primes存到了第几位
  st[1]=1;
  for(int i=2;i<N;i++)
  {
      if(!st[i]) primes[cnt++]=i;
      for(int j=0;primes[j]*i<N;j++)//从第一个数开始遍历prime
      {
          //原则：每个合数只被它最小的质因数筛掉
          st[primes[j]*i] st[primes[j]*i]=1;
          //if(i%primes[j]!=0) i中的最小质因子都比prime[j]大，
          //那么i*primes[j]的最小质因子一定是primes[j]
          if(i%primes[j]==0) break; 
  	}
  }
  ```

- AC代码

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e5+10;
  int m,n,cnt;
  int st[N],prime[N];
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n>>m;
  	vector<int> ans;
  	set<int>factor;
  	
  	st[1]=1;
  	for(int i=2;i<N;i++)
  	{
  		if(!st[i]) prime[cnt++]=i;
  		for(int j=0;i*prime[j]<N;j++)
  		{
  			st[prime[j]*i]=1;
  			if(i%prime[j]==0) break;
  		}
  	}
  	
  	for(int i=0;i<n;i++)
  	{
  		int x;
  		cin>>x;
  		for(auto it: prime)
  		{
  			if(it>x)
  			break;
  			if(x%it==0)
  			factor.insert(it);
  		}
  	}
  	
  	for(int i=1;i<=m;i++)
  	{
  		bool f=1;
  		for(auto it:factor)
  		{
  			if(it>i)
  			break;
  			if(i%it==0)
  			{
  				f=0;
  				break;
  			}
  		}
  		if(f)
  		ans.push_back(i);
  	}
  	
  	cout<<ans.size()<<'\n';
  	for(auto it:ans)
  	cout<<it<<'\n';
  	
  	return 0;
  }
  ```
  

## 807 排队

```C++
#include <iostream>
#include <algorithm>
using namespace std;
const int max_n = 1e5;
const long long mod_num = 1e8 - 7;
struct node { 
int idx, val; 
} a[max_n], b[max_n];
long long ans = 0;
int temp[max_n];

void merge_sort(node arr[], int l, int r) {
	if (l == r) return;

	int m = l + r >> 1;
	merge_sort(arr, l, m);
	merge_sort(arr, m + 1, r);

	long long i = l, j = m + 1, k = l;
	while (i <= m && j <= r) {
		if (arr[i].val > arr[j].val) {
			temp[k] = arr[j].val;
			j++, k++;
			ans = (ans + m - i + 1LL) % mod_num;//累计逆序数
		}
		else {
			temp[k] = arr[i].val;
			i++, k++;
		}
	}

	while (i <= m) temp[k++] = arr[i++].val;
	while (j <= r) temp[k++] = arr[j++].val;
	for (i = l; i <= r; i++) arr[i].val = temp[i];
}

int main() {
	int n;
	cin >> n;
	for (int i = 0; i < n; i++) {
		//scanf("%d", a + i);
		cin >> a[i].val;
		a[i].idx = i;
	}
	for (int i = 0; i < n; i++) {
		//scanf("%d", b + i);
		cin >> b[i].val;
		b[i].idx = i;
	}

	sort(a, a + n, [](node n1, node n2) {
		return n1.val < n2.val;
		});
	sort(b, b + n, [](node n1, node n2) {
		return n1.val < n2.val;
		});

	for (int i = 0; i < n; i++) {
		a[i].val = b[i].idx;
	}
	sort(a, a + n, [](node n1, node n2) {
		return n1.idx < n2.idx;
		});

	merge_sort(a, 0, n - 1);
	cout << ans << endl;
	return 0;
}
```

## 901 最短路径计数

- 图的存储：用vector
- 广度优先搜索：每次遍历时判断：如果这个点是第一次遍历，用vis标记，并用dis标记最短路程为当前步数（前一个点的最短步数+1）；如果已经遍历过，则判断此时是否为最短路径，如果是，ans++；

```C++
#include<bits/stdc++.h>
using namespace std;
const int MOD=100003;
const int N=1e6+10;
int n,m;
int ans[N],vis[N],dis[N];
vector<int> mp[N];
signed main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	
	cin>>n>>m;

	while(m--)
	{
		int x,y;
		cin>>x>>y;
		mp[x].push_back(y);
		mp[y].push_back(x);
	}
	queue<int> q;
	q.push(1);
	vis[1]=1;
	ans[1]=1;
	while(!q.empty())
	{
		int now=q.front();
		q.pop();
		for(int i=0;i<mp[now].size();i++)
		{
			int v=mp[now][i];
			if(!vis[v])
			{
				dis[v]=dis[now]+1;
				vis[v]=1;
				q.push(v);
			}
			if(dis[v]==dis[now]+1)
			ans[v]=(ans[v]+ans[now])%MOD;
		}
	}
	for(int i=1;i<=n;i++)
	cout<<ans[i]<<'\n';
	return 0;
}
```







## 902 最后的舞会

- n的范围很小：直接搜索每一对去匹配加一个小剪枝

- 注意：pow(2, 30) 在int范围内，开long long会慢不少

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  bool vis[20];
  int a[20][20];
  int n,ans;
  void dfs(int step,int res) //step为当前已经匹配的对数，res为当前异或和
  {
  	if(step==n)
  	{
  		ans=max(ans,res);
  		return ;
  	 } 
  	 int i;
  	 for(i=2;i<=2*n-1;i++)
  	 {
  	 	if(!vis[i])
  	 	break;
  	 }
  	 for(int j=i+1;j<=2*n;j++)
  	 {
  	 	if(vis[j]==0)
  	 	{
  	 		vis[i]=1,vis[j]=1; //匹配i j
  			 dfs(step+1,res^a[i][j]);
  			 vis[i]=0,vis[j]=0; 
  		 }
  	 }
  	 
   } 
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	for(int i=1;i<=2*n-1;i++)
  	{
  		for(int j=i+1;j<=2*n;j++)
  		cin>>a[i][j];
  	}
  	
  	vis[1]=1;
  	
  	for(int i=2;i<=2*n;i++)
  	{
  		vis[i]=1;
  		dfs(1,a[1][i]);
  		vis[i]=0;
  	}
  	cout<<ans;
  	return 0;
  }
  ```

  

## 903 倒数第n个字符串

- 进制转换题，转成26进制，然后求pow(26, l)对应的26进制数

- 担心超时用了快速幂

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  char zhuan[30]="abcdefghijklmnopqrstuvwxyz"; 
  ll l,n,res;
  ll quickpow(ll a,ll b)
  {
  	ll sum=1;
  	while(b)
  	{
  		if(b&1)
  		sum=sum*a;
  		a=a*a;
  		b>>=1;
  	}
  	return sum;
  }
  signed main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>l>>n;
  	res=quickpow(26,l)-n;
  	
  	string ans;
  	while(l--)
  	{
  		ans=zhuan[res%26]+ans;
  		res/=26;
  	}
  	
  	cout<<ans<<'\n';
  	
  	return 0;
  }
  ```