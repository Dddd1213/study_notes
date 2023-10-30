# Week 15

[TOC]

# div2 每日一题

## 305 删删

- 暴力

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int t;
  int main()
  {
  	cin.tie(0);
  	cout.tie(0);
  	cin>>t;
  	while(t--)
  	{
  		int n,ans=1e9,sum=0;
  		string s;
  		cin>>n>>s;
  		for(char i='a';i<='z';i++)
  		{
  			bool flag=true;
  			int l=0,r=n-1,sum=0;
  			while(l<r)
  			{
  				if(s[l]==s[r]) l++,r--;
  				else
  				{
  					if(s[l]==i) l++,sum++;
  					else if(s[r]==i) r--,sum++;
  					else
  					{
  						flag=false;
  						break;
  					}
  				}
  			}
  			if(flag)
  			ans=min(ans,sum);
  		}
  		if(ans==1e9) cout<<"-1"<<endl;
  		else cout<<ans<<endl;
  	}
  	return 0;
  }
  ```



## 306 快快变大（区间dp）

- dp[i][j]=max(dp[i][j],dp[i][k]+dp[k+1][j]+cost)

  cost指代把dp[i][k] 和 dp[k+1][j] 的两个[区间合并](https://so.csdn.net/so/search?q=区间合并&spm=1001.2101.3001.7020)起来的花费

- 注意i k合并后的数值要预处理不然可能会超时

```C++
#include <bits/stdc++.h>
#define ll long long
using namespace std;
const int mx=1e3+5;
const int INF=0x3f3f3f3f;
ll n,t=0;
ll f[301][301],aa[301][301];
int main()
{
    cin.tie(0);
    cout.tie(0);
    cin>>n;
    ll a[n+1];
    for(int i=1;i<=n;i++)
    {
        scanf("%ld",&a[i]);
        f[i][i]=0;
        aa[i][i-1]=1;
    }
    for(int i=2;i<=n;i++)
    {
        f[i-1][i]=pow(a[i-1]-a[i],2);
    }
    for(int i=1;i<=n;i++)
    {
        for(int j=i;j<=n;j++)
        {
            aa[i][j]=aa[i][j-1]*a[j]%1000003;
        }
    }
    for(int l=2;l<=n;l++)
    {
        for(int i=1;i+l-1<=n;i++)
        {
            int j=i+l-1;
            for(int k=i;k<j;k++)
            {
                ll tp3=pow(aa[i][k]-aa[k+1][j],2);
                f[i][j]=max(f[i][k]+f[k+1][j]+tp3,f[i][j]);
            }
        }
    }
    printf("%ld",f[1][n]);
}
```



## 307 饿饿 饭饭2

- 看的大佬同学写的题解QAQ

- 暴力，最后的结果是最大值的pow(2,x)*pow(3,y)倍，枚举即可

  ```C++
  #include <bits/stdc++.h>
  #define ll double
  using namespace std;
  const int mx=1e3+5;
  const int INF=0x3f3f3f3f;
  int n,t=0;
  ll f[301][301];
  bool checkc(ll x)
  {
      int i=0,j=0;
      int e[2]={2,3};
      while(x!=1)
      {
          if(i>1)
          {
  		i=0;
  		}
          if(fmod(x,e[i]))
          {
  		i++;
  		j++;
  		}
          else
          {
  		x/=e[i];
  		j=0;
  		}
          if(j==2)
          {
  		return 0;
  		}
      }
      return 1;
  }
  int main()
  {
      cin.tie(NULL);
      vector <ll> b;
      for(int i=0;i<35;i++)
      {
          for(int j=0;j<35;j++)
          {
              b.push_back(pow(2,i)*pow(3,j));
          }
      }
      sort(b.begin(),b.end());
      cin>>t;
      for(int q=0;q<t;q++)
      {
          cin>>n;
          ll a[n];
          ll mxx=0;
          for(int i=0;i<n;i++)
          {
              cin>>a[i];
              mxx=max(mxx,a[i]);
          }
          int flag=0;
          for(int i=0;i<b.size();i++)
          {
              for(int j=0;j<n;j++)
              {
                  if(fmod(mxx*b[i],a[j])!=0)
                  {
                      break;
                  }
                  double tp=mxx*b[i]/a[j];
                  if(checkc(tp))
                  {
                      flag++;
                  }
              }
              if(flag==n)
              {
                  break;
              }
              flag=0;
          }
          if(flag)
          cout<<"YES"<<endl;
          else
          cout<<"NO"<<endl;
      }
  }
  ```



## 401 子串分值和

- 蓝桥杯原题

- 思路：该字母只有第一次出现时是有价值的，我们需要寻找的是该字母作为第一次出现的字母被多少子串包含

- 实现：用last[s[i]]记录s[i]上一次出现的位置。

  下一次遇到该字母时，向左有i-last[i]个字母，向右有len-i+1个字母，也就是共被(i-last[i])*(len-i+1)个子串包含。

- 记得开longlong

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  string s;
  long long len,ans;
  map<char,int>mp;
  int main()
  {
  	cin>>s;
  	s=' '+s;
  	len=s.length();
  	for(int i=1;i<=len;i++)
  	{
  		ans+=(len-i)*(i-mp[s[i]]);
  		mp[s[i]]=i;
  	}
  	cout<<ans<<endl;
  	return 0;
  }
  ```

  

## 402 蒟蒻

- 审题啊！！！！这样比赛罚时会罚疯掉的！！！

- 价格或口感，或口感，或！！！！！有一个一样都不行！！！！！！

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  long long n,ans;
  int w1[N],t1[N];
  struct GD{
  	int w,t;
  };
  bool jg(GD a,GD b)
  {
  	return a.w <b.w; 
  }
  bool kg(GD a,GD b)
  {
  	return a.t <b.t ;
  }
  int main()
  {
  	cin>>n;
  	vector<GD>gd; 
  	for(int i=0;i<n;i++)
  	{
  		int op,w,t;
  		cin>>op;
  		if(op==1)
  		{
  			cin>>w>>t;
  			if(!w1[w]&&!t1[t])
  			{
  				w1[w]=1;
  				t1[t]=1;
  				GD gdn={w,t};
  				gd.push_back(gdn);
  				ans+=w;
  			}
  		}
  		if(op==2&&!gd.empty())
  		{
  			sort(gd.begin(),gd.end(),jg);
  			ans-=gd[0].w;
  			w1[gd[0].w]=0;
  			t1[gd[0].t]=0;
  			gd.erase(gd.begin());
  		}
  		if(op==3&&!gd.empty())
  		{
  			sort(gd.begin(),gd.end(),kg);
  			ans-=gd[0].w;
  			w1[gd[0].w]=0;
  			t1[gd[0].t]=0;
  			gd.erase(gd.begin());
  		}
  	}
  	cout<<ans<<endl;
  	return 0;
  }
  ```

  

## 403 锦标赛

- 莫名简单的题目

- 思路：现将a[i]升序排列，然后从后往前遍历，如果a[i]-a[i-1]超过k，则i必赢，那么i-1往前（一定比a[i-1]小）必输。

  设初始ans=n，后面减就好了

  ```C++
  #include<bits/stdc++.h>
  #define ll long long
  using namespace std;
  const int N=1e5+10;
  ll n,k,ans;
  ll a[N];
  int main()
  {
  	 cin>>n>>k;
  	 ans=n;
  	 for(int i=0;i<n;i++)
  	 cin>>a[i];
  	 sort(a,a+n);
  	 for(int i=n-1;i>0;i--)
  	 {
  	 	if(a[i]-a[i-1]>k)
  	 	{
  	 		ans-=i;
  	 		break;
  		 }
  	 }
  	 cout<<ans<<endl;
  	return 0;
  }
  ```

  

## 404 可重排列

- 全排列函数：next_permutation(v.begin(),v.end(),cmp);

- cmp是bool型，和sort那个差不多

-  ~~这种写法超时了（？）~~超时原因是cin和cout，加cin.tie(0)也没用（生气）

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int n;
  vector<int>v;
  int main()
  {
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		int x;
  		cin>>x;
  		for(int j=0;j<x;j++)
  		{
  			v.push_back(i);
  		}
  	}
  
  	do
  	{
  		for(auto it:v)
  		{
  			cout<<it<<" ";
  		}
  		cout<<endl;
  	}
  	while(next_permutation(v.begin(),v.end()));
  
  	return 0;
  }
  ```

  

## 405 进制转换

```C++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
ll n,m,sum;
string s;
char a[100];
double b[1000];
int main()
{
	a[10]='A';
	b['A']=10;
	for(int i=11;i<=35;i++)
	{
		a[i]=a[i-1]+1;
		b[a[i]]=i;
	}

	a[36]='a';
	b['a']=36;
	for(int i=37;i<=61;i++)
	{
		a[i]=a[i-1]+1;
		b[a[i]]=i;
	}

	cin>>n>>m;
	while(n--)
	{
		double t,len;
		string x;
		cin>>t>>x;
		len=x.length();
		for(int i=len-1;i>=0;i--)
		{
			if(x[i]>='0'&&x[i]<='9')//这里0和9忘记加单引号debug了一年、、、
			sum+=((int)(x[i]-'0'))*pow(t,len-1-i);
			else
			sum+=b[x[i]]*pow(t,len-1-i);
		
		}
	}

		while(sum)
		{
			if(sum%m>=10)
			s=a[sum%m]+s;
			else
			s=(char)(sum%m+'0')+s;
			sum/=m;
		}
		cout<<s<<endl;
	return 0;
}
```



## 406 循环子串

- 只需要判断该字符串翻转后是不是循环子串即可，如果整个字符串翻转后都满足，它的子串也一定都满足

- find()函数运用，找不到返回-1

- reverse()函数运用

- 注意加ios::sync.....这段之后，cout和printf 就不能混用了

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int t;
  int main()
  {
  	ios::sync_with_stdio(false);
  	cin.tie(0);
  	cout.tie(0);
  	cin>>t;
  	while(t--)
  	{
  		int n;
  		string s,s2;
  		cin>>n>>s;
  		s2=s;
  		s+=s;
  		reverse(s2.begin(),s2.end());
  		
  		if(s.find(s2)!=-1)
  		cout<<"YES"<<'\n';
  		else
  		cout<<"NO"<<'\n';
  	}
  	return 0;
   } 
  ```




## 407 饿饿 饭饭之暑假大狂欢

- 判断这个人拥有的数字是不是其他人的子序列，如果是则另一个人一定赢不了

```C++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	std::ios::sync_with_stdio(false);
	cin.tie(0);
	cout.tie(0);
	int t;
	bool ans[101]={false};
	vector<vector<int> >a(101);

	cin>>t;
	for(int i=1;i<=t;i++)
	{
		int len;
		cin>>len;
		for(int j=1;j<=len;j++)
		{
			int x;
			cin>>x;
			a[i].push_back(x);
		}
	}
	for(int i=1;i<=t;i++)
	{
		bool flag1=0;	
		for(int j=1;j<=t;j++)
		{
			if(j==i||a[j].size()>a[i].size())continue;
			bool flag=0;
			for(int k=0;k<a[j].size();k++)
			{
				if(find(a[i].begin(),a[i].end(),a[j][k])==a[i].end())
				{
					flag=1;
					flag1=1;
					break;
				}
			}
			if(flag==0)	
			{
				flag1=1;
				ans[i]=false;
				break;
			}
			else
            {
                flag1=1;
                ans[i]=true;
            }
		}
		if(flag1==0)ans[i]=true;
	}

	for(int i=1;i<=t;i++)
		if(ans[i]==true)
			cout<<"YES"<<'\n';
		else
			cout<<"NO"<<'\n';
	return 0;
}
```



## 501 RSA

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

  

## 503 A-B 数对

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







# 刷题

## 构造回文

- 题目：给定一个字符串s，可以从中删去一些字符，使得剩下的串是一个回文串。如何删除才能使回文串最长。
- 思路：用s2存翻转后的字符串，找s和s2的最长公共子串（动态规划）



## P3397 地毯（前缀和与差分）

- 只需要记录地毯边界就行

```C++
#include<bits/stdc++.h>
using namespace std;
const int N=1010;
int n,m;
int mp[N][N];
int main()
{
	cin>>n>>m;
	while(m--)
	{
		int x1,y1,x2,y2;
		cin>>x1>>y1>>x2>>y2;
		for(int i=x1;i<=x2;i++)
		{
			mp[i][y1]++;
			mp[i][y2+1]--;
		}
	}
	int x=0;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<n;j++)
		{
			x+=mp[i][j];
			cout<<x<<" ";
		}
		cout<<x+mp[i][n]<<endl;
		x=0;
	}
	return 0;
}
```



## P3406 海底高铁（前缀和与差分）

- 刚开始用暴力存的每条路要走多少遍，超时了

```C++
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+10;
int m,n,ans,a,b;
int path[N];//path[i]表示第i段路需要经过几次 
int main()
{
	cin>>n>>m;
	cin>>a; 
	bool flag=1;
	for(int i=1;i<m;i++)
	{
		cin>>b;
		if(a>b)
		{
			 swap(a,b);
			 flag=0;
		 }
		for(int j=a;j<b;j++)
		path[j]++;
		if(flag) a=b;
		flag=1;
	}

	int x,y,z;
	for(int i=1;i<n;i++)
	{
		cin>>x>>y>>z;
		ans+=min(path[i]*x,path[i]*y+z);
	}
	cout<<ans<<endl;	
	return 0;
}
```

- 改用前缀和写，注意要用long long

  对于每一段路径，小的数+1，大的数-1
  
  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e5+10;
  long long n,m,pre,now,ans;
  long long a[N];
  int main()
  {
  	cin>>n>>m;
  	cin>>pre;
  	m--; 
  	while(m--)
  	{
  		cin>>now;
  		if(pre>now) a[pre]--,a[now]++;
  		else a[pre]++,a[now]--;
  		pre=now;
  	}
  	for(int i=2;i<=n;i++)
  	a[i]+=a[i-1];
  	
  	for(int i=1;i<n;i++)
  	{
  		int x,y,z;
  		cin>>x>>y>>z;
  		ans+=min(x*a[i],y*a[i]+z);
  	}
  	cout<<ans<<endl;
  	return 0;
  }
  ```
  
  

## P1719 最大加权矩形

- 拿到题目先看数据范围1~120 -> 可以用暴力 

- 用左上和右下的坐标来确定当前矩形，即(i, j)和(k, l)，用大矩形减小矩形得到当前矩形内数字和

  ```
  #include<bits/stdc++.h>
  using namespace std;
  int n,ma=-999999;
  int a[150][150],sum[150][150];
  int main()
  {
  	ios::sync_with_stdio(false);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=n;j++)
  		{
  			cin>>a[i][j];
  			a[i][j]+=a[i][j-1];
  			sum[i][j]=a[i][j]+sum[i-1][j];
  		}
  	}
  	
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=n;j++)
  		{
  			for(int k=i;k<=n;k++)
  			{
  				for(int l=j;l<=n;l++)
  				{
  					ma=max(ma,sum[k][l]-sum[k][j-1]-sum[i-1][l]+sum[i-1][j-1]);
  				}
  			}
  		}
  	}
  	cout<<ma<<endl;
  	return 0;
  }
  ```

  

## P4715 淘汰赛(二叉树)

- 思路：只需要亚军，那就是左半边的最大值和右半边的最大值比较，小的即为ans

- 注意：需要输出的是编号不是值，这里用map存一下编号

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int n,c,d;
  int a[20000];
  map<int,int>mp;
  int main()
  {
  	ios::sync_with_stdio(false);
  	cin.tie(0);
  	cout.tie(0);
  	
  	cin>>n;
  	int js=pow(2,n);
  	for(int i=0;i<js;i++)
  	{
  		cin>>a[i];
  		mp[a[i]]=i+1;
  	}
  	sort(a,a+js/2);
  	sort(a+js/2,a+js);
  	c=a[js/2-1];
  	d=a[js-1];
  	if(c>d)
  	cout<<mp[d]<<endl;
  	else
  	cout<<mp[c]<<endl;
  	return 0;
  }
  ```

  



















































