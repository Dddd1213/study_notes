## Week 16

[TOC]

## div2 代码源每日一题

### 501 RSA

- 质数正常判断即可

- A*B大于1的整数的平方的整数倍可以转换成 A和B有公因子 或 A或B中有因子是某个大于1的整数的平方的整数倍

  ```C++
  #include<iostream>
  #include<cmath>
  #include<map>
  using namespace std;
  bool isprime(long long x)	//判断是不是质数
  {
  	if(x==1)return 0;
  	for(int i=2;i<=sqrt(x);i++)
  	{
  		if(x%i==0)return 0;
  	}
  	return 1;
  }
  bool flag=0;
  map<long long,int>mymap;
  bool check(long long a)
  {
  	for(int i=2;i<=sqrt(a);i++)
  	{
  		if(a%i==0)
  		{
  			mymap[i]++;
  			mymap[a/i]++;
  			if(mymap[i]>=2||mymap[a/i]>=2)
  			{
  				flag=1;	
  				return 1;
  			}
  		}
  	}
  }
  int main()
  {
  	std::ios::sync_with_stdio(false);
  	cin.tie(0);
  	cout.tie(0);
  	long long a,b;
  	
  	cin>>a>>b;
  	if(a==b)
  	{
  		cout<<"no credit";
  		return 0;
  	}
  	if(isprime(a)&&isprime(b))
  	{
  		cout<<"full credit";
  		return 0;
  	}
  	check(a);
  	check(b);
  	if(flag==1)
  	{
  		cout<<"no credit";
  	}
  	else
  	{
  		cout<<"partial credit";
  	}
  	return 0;
  }
  
  ```

  

### 503 A-B 数对

- A-B=C转换成A-C=B，用map存B

```C++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
const int N=2e5+10;
int n,c,ans;
ll a[N];
map<int,int>mp;
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0);
	cout.tie(0);
	cin>>n>>c;
	for(int i=0;i<n;i++)
	{
		cin>>a[i];
		mp[a[i]]++;//存的是b 
		a[i]-=c; //A-C 
	}
	
	for(int i=0;i<n;i++)
	{
		ans+=mp[a[i]];
	}
	cout<<ans<<endl;
	return 0;
 } 
```



### 504 数位计算

- 1~9,10~99,100~999，每个位数一样的为一档，同一档中，f(n)-f(n-1)=1

  等差数列求和

- 思路：先求出n的最大位数，然后再处理，记得每一步都要取模

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  const int MOD=998244353;
  ll ans,n1;
  string n;
  int main()
  {
  	ios::sync_with_stdio(false);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	int len=n.length();
  	for(int i=0;i<len;i++)
  	n1=n1*10+(ll)(n[i]-'0');
  	if(n1<10)
  	{
  		ans+=(1+n1)*n1/2;
  		cout<<ans<<'\n';
  		return 0;
  	}
  	ll k=pow(10,len-1);
  	ans+=45;
  	ll x=(n1-k+2)%MOD;
  	ll y=(n1-k+1)%MOD;
  	ans+=(x*y/2)%MOD;
  	while(k/10>=10)
  	{
  		x=(k-k/10+1)%MOD;
  		y=(k-k/10)%MOD;
  		ans+=(x*y/2)%MOD;
  		k/=10;
  	}
  	cout<<ans%MOD<<'\n';
  	return 0;
  }
  ```

  

### 505 新国王游戏

- 两两之间，判断a在b前面得到的结果大 还是 b在a前面得到的结果大

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  const ll MOD=1000000007;
  const ll N=1e6+10;
  ll n,ans=0,sum=1;
  struct Peo{
  	ll a,b;
  }peo[N];
  
  bool cmp(Peo a,Peo b)
  {
  	return b.b +a.b *b.a  < a.b +b.b *a.a  ;
  }
  
  int main()
  {
  	ios::sync_with_stdio(false);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	for(int i=0;i<n;i++)
  	{
  		cin>>peo[i].a >>peo[i].b ;
  		peo[i].a%=MOD;
  		peo[i].b%=MOD;
  	}
  	
  	sort(peo,peo+n,cmp);
  	ans+=peo[0].b ;	
  
  	for(int i=0;i<n;i++)
  	{
  		if(i>0)
  		ans=(ans+(sum*peo[i].b )%MOD)%MOD;
  		ans%=MOD;
  		sum=(sum*peo[i].a )%MOD;
  	}
  	
  	cout<<ans%MOD<<'\n';
  	return 0;
  }
  ```

  

### 506 完美数

- x个a和(m-x)个b排列组合，用sum记录x个a和(m-x)个b的和，然后再判断sum是不是由a和b组成即可
- 注意有重复元素的排列组合公式

```C++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
typedef pair<ll, ll>PII;
const int MOD = 1e9 + 7, N = 1e6 + 10;
ll fact[N], infact[N];

ll qmi(int a, int b)
{
    ll res = 1;
    while (b)
    {
        if (b & 1) res = res * a % MOD;
        a = a * (ll)a % MOD;
        b >>= 1;
    }
    return res;
}

void init()
{
    fact[0] = infact[0] = 1;
    for (int i = 1; i < N; i++)
        fact[i] = fact[i - 1] * i % MOD;

    infact[N - 1] = qmi(fact[N - 1], MOD - 2);
    for (int i = N - 2; i; i--)
        infact[i] = infact[i + 1] * (i + 1) % MOD;
}

int C(int a, int b)
{
    return (fact[a] * infact[b] % MOD * infact[a - b] % MOD) % MOD;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    int a, b, m;
    cin >> a >> b >> m;
    init();
    char c = a + '0', d = b + '0';
    int x;
    ll res = 0;
    for (int i = 0; i <= m; i++)
    {
        x = m - i;
        ll num = x * a + i * b;
        bool flag = true;
        while (num)
        {
            if (num % 10 != a && num % 10 != b)
            {
                flag = false;
                break;
            }
            num /= 10;
        }
        if (!flag)continue;
        res = (res + C(m,i)) % MOD;
    }
    cout << (res%MOD) << endl;
    return 0;
}
```



### 507 Lusir的游戏

- 二分答案

- 注意：如果e>h_max，e就会不断增加，后面可能会超long long，所以check里加一个判断return true

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  const int N=1e5+10;
  ll n,l=1,r,ans,max_value;
  ll h[N];
  bool check(ll e)
  {
  	for(int i=1;i<=n;i++)
  	{
  		if(h[i]>e)
  		e-=h[i]-e;
  		else
  		e+=e-h[i];
  		if(e<0)
  		return false;
  		if(e>=max_value) return true;
  	}
  	return true;
  }
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	
  	for(int i=1;i<=n;i++)
  	{
  		cin>>h[i];
  		r+=h[i];
  		max_value=max(h[i],max_value);
  	}
  	
  	while(l<=r)
  	{
  		ll mid=l+(r-l)/2;
  		if(check(mid)) 
  		{
  			ans=mid;
  			r=mid-1;
  		}
  		else 
  		l=mid+1;
  	}
  	cout<<ans<<'\n';
  	return 0;
  }
  ```

  

### 601 BFS练习1（广度优先）

- 每一个数字对应四种可能，一层一层搜树，搜到需要的数字就把答案存下来。当存了q个时，结束。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=300050;
  int vis[N],ans[N];
  int step,a,q;
  int main()
  {
  	ios::sync_with_stdio(0);
  	cin.tie(0);
  	cout.tie(0);
  	
  	queue<int> que;
  	
  	cin>>a>>q;
  	que.push(a);
  	vector<int> v(q);
  	for(int i=0;i<q;i++)
  	{
  		cin>>v[i];
  		ans[v[i]]=-1;
  	}
  	
  	while(!que.empty()&&q)
  	{
  		int len=que.size(); //important!!!这样保证每一层step+1 ,而不是每push一次step+1 
  		for(int i=0;i<len;i++)
  		{ 
  		int now=que.front();
  		que.pop();
  		if(ans[now]==-1)
  		{
  			ans[now]=step;
  			q--;
  		}
  		if(now*3<N&&vis[now*3]==0)
  		{
  			que.push(now*3);
  			vis[now*3]=1;
  		}
  		if(now*2<N&&vis[now*2]==0)
  		{
  			que.push(now*2);
  			vis[now*2]=1;
  		}
  		if(now+1<N&&vis[now+1]==0)
  		{
  			que.push(now+1);
  			vis[now+1]=1; 
  		}
  		if(now-1&&vis[now-1]==0)
  		{
  			que.push(now-1);
  			vis[now-1]=1;
  		}
  	}
  		step++;
  		
  	}
  	
  	for(auto i:v)
  	{
  		cout<<ans[i]<<" ";
  	}
  	
  	return 0;
  }
  ```

  

### 602 01序列 2

- 从0到n枚举1的数量，0的数量为(i-1)*k

  删掉每两个1之间的k个0，该01串还剩n-(i-1)*k个，

  默认每两个1之间都有k个0，这些是固定的。那么01串的长度就是n-(i-1)*k个。

  C(i)(n-(i-1)*k)

- 注意1：除法不能取模，引入**逆元**

  - 概念：在mod p的意义下， 有(a*b) mod p = 1，则称b为a的乘法逆元，为方便我们记作inv(b)；

  - 性质：**a/b mod p == a*inv(b) mod p**

  - 求逆元：费马小定理：pow(b, p-1) mod p =1

    所以**pow(b, p-2)**即为在mod p意义下，b的乘法逆元

    ```C++
    ll inv(ll a)
    {
    	return pow(a,MOD-2)%MOD;
    }
    ```

- 注意2：**快速幂**

  ```C++
  ll quick_pow(ll a, ll b)
  {
  	ll ans=1;
  	while(b)
  	{
  		if(b&1)//判断奇偶，偶为0，奇为1
  		ans=ans*a%MOD;
  		a=a*a%MOD;
  		b>>=1;
  	}
  	return ans;
  }
  ```

- AC代码

  ```C++
  #include <iostream>
  using namespace std;
  const int max_n = 1e6;
  const int max_k = max_n;
  const long long mod_num = 1e9 + 7;
  
  int n, k;
  long long factorial[max_n + 1];
  
  long long pow(long long x, long long y, long long p) {
  	long long ret = 1;
  	while (y) {
  		if (y & 1) ret = (ret * x) % p;
  		x = (x * x) % p;
  		y >>= 1;
  	}
  	return ret;
  }
  
  long long inv(long long x, long long p) {
  	return pow(x, p - 2, p);
  }
  
  long long cmd(long long m, long long n) {
  	long long num1 = factorial[n];
  	long long num2 = factorial[m];
  	long long inv2 = inv(num2, mod_num);
  	long long num3 = factorial[n - m];
  	long long inv3 = inv(num3, mod_num);
  	return ((num1 * inv2) % mod_num) * inv3 % mod_num;
  }
  
  int main() {
  	factorial[0] = 1;
  	for (int i = 1; i <= max_n; i++) factorial[i] = factorial[i - 1] * i % mod_num;
  
  	cin >> n >> k;
  	int max_ones = (n + k) / (k + 1);
  	long long ans = 1;
  	for (int i = 1; i <= max_ones; i++) {
  		int left = n - (i - 1) * k + 1;
  		ans = (ans + cmd((i + 1) - 1, left - 1)) % mod_num;
  	}
  	cout << ans << endl;
  	return 0;
  }
  ```

  

### 603 整除光棍

- 想麻烦了一点写了个高精度除法

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  string s;
  int js;
  int ss[N],res[N];
  bool check(int x,int len)
  {
  	long long yu=0;
  	for(int i=0;i<len;i++)
  	ss[i]=(int)(s[len-i-1]-'0');
  	
  	for(int i=0;i<len;i++)
  	{
  		yu=yu*10+ss[i];
  		if(yu>=x)
  		{
  			res[js]=yu/x;
  			yu=yu-x*res[js];
  		}
  		else
  		res[js]=0;
  		js++;
  	}
  	
  	if(yu==0)
  	return true;
  	else
  	return false;
  }
  
  
  int main()
  {
  	int len=0,x;
  	cin>>x;
  	while(1)
  	{
  		ss[N]={0};
  		res[N]={0};
  		js=0;
  		len++;
  		s+='1';
  		if(check(x,len))
  		{
  			int i=0;
  			while(res[i]==0)
  			{
  				i++;
  			}
  			
  			for(;i<js;i++)
  			cout<<res[i];
  			cout<<" "<<len;
  			break;
  		}
  	}
  	return 0;
   } 
  ```

- 简化：

  ```C++
  #include<stdio.h>
  int main(){
  	int s=1,n,num=0,k=0;
  	scanf("%d",&n);
  	while(1){
  		if(s/n!=0)k=1;
  		num++;
  		if(k)printf("%d",s/n);
  		s=s%n;
  		if(s==0)break;
  		s=s*10+1;
  	}
  	printf(" %d",num);
  }
  ```

  

### 604 碰撞

```C++
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+10;
int n;
struct Peo{
	int x,y;
	char dir;
}peo[N];
bool cmp(Peo a,Peo b)
{
	if(a.y ==b.y )
	return a.x <b.x ; //按x升序 
	return a.y >b.y ; //按y降序 
}
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	
	cin>>n;
	
	for(int i=0;i<n;i++)
	{
		cin>>peo[i].x>>peo[i].y ;
	}
	for(int i=0;i<n;i++)
	{
		cin>>peo[i].dir ;
	}
	
	sort(peo,peo+n,cmp);
	for(int i=1;i<n;i++)
	{
		if(peo[i].y==peo[i-1].y&&peo[i].dir =='L'&&peo[i-1].dir=='R' )
		{
			cout<<"Yes";
			return 0; 
		}
		
	 } 
	
	cout<<"No";
	return 0;
}
```

















## 刷题

### P4913 二叉树深度

- 基础树的存储和遍历

- 存储用node数组

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  int ans,n;
  struct node{
  	int left,right; //左结点编号和右结点编号 
  }tree[N];
  
  void dfs(int id, int deep)
  {
  	if(id==0) return ;
  	ans=max(ans,deep);
  	dfs(tree[id].left ,deep+1);
  	dfs(tree[id].right ,deep+1);
  }
  
  int main()
  {
  	ios::sync_with_stdio;
  	cin.tie(0);
  	cout.tie(0);
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	cin>>tree[i].left >>tree[i].right ;
  	dfs(1,1);
  	cout<<ans<<endl;
  	return 0;
   } 
  ```

  



















