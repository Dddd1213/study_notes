# Week 19

[TOC]

# 代码源每日一题

## 904 排队（并查集）

两种情况无法排成一排

- 一个人需要和三个及以上人相邻 -> 用cnt记录和每个人相邻的人的数量，超过2就f=0
- 形成环（如1 2相邻，1 3相邻，2又要和3相邻） -> 并查集，有相同根f=0

```C++
#include<bits/stdc++.h>
using namespace std;
unordered_map<int,int>cnt,mp;
int findRoot(int x)
{
	if(mp[x]!=x)
	mp[x]=findRoot(mp[x]);
	return mp[x];
}
void unionRoot(int a,int b)
{
	int x=findRoot(a);
	int y=findRoot(b);
	if(x==y) return ;
	if(x>y) swap(x,y);//大的为根
	mp[x]=y;
	return ; 
	
}
int main()
{	
	int n,m;
	bool f=1;
	cin>>n>>m;
	for(int i=1;i<=n;i++)
	{
		mp[i]=i;
	 } 
	while(m--)
	{
		int x,y;
		cin>>x>>y;
		cnt[x]++;
		cnt[y]++;
		if(findRoot(x)==findRoot(y))
		f=0;
		unionRoot(x,y);
		if(cnt[x]>2||cnt[y]>2)
		f=0;
	}
	if(f)
	cout<<"Yes";
	else
	cout<<"No";
	return 0;
 } 
```



# 洛谷题库

## P1003 [NOIP2011 提高组] 铺地毯

- 思路1：用mp\[N][N]直接存，但是N最大能到1e5，开二维数组超内存了，否掉

- 思路2：用Carpet结构体直接存地毯的参数，输入x，y后从前往后（大i覆盖小i）扫一遍所有地毯，得ans

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e5+10;
  struct Carpet{
  	int a,b,j,k;
  }carpet[N];
  int main()
  {
  	int n,ans=-1;
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		cin>>carpet[i].a>>carpet[i].b>>carpet[i].j>>carpet[i].k;
  	}
  	int x,y;
  	cin>>x>>y;
  	
  	for(int i=1;i<=n;i++)
  	{
  		if(x>=carpet[i].a &&x<=carpet[i].a+carpet[i].j&&y>=carpet[i].b &&y<=carpet[i].b +carpet[i].k)
  		ans=i;
  	}
  	
  	cout<<ans;
  	return 0;
  }
  ```

  

## P1008 [NOIP1998 普及组] 三连击

-  暴力

- tips：两个集合内所有数相加相乘结果一样，两个集合的内容一样

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int main()
  {
  	
  	for(int i=123;i<=329;i++)
  	{
  		int a=i,b=i*2,c=i*3;
  		if((a/100+a/10%10+a%10+b/100+b/10%10+b%10+c/100+c/10%10+c%10==1+2+3+4+5+6+7+8+9)&&((a/100)*(a/10%10)*(a%10)*(b/100)*(b/10%10)*(b%10)*(c/100)*(c/10%10)*(c%10)==1*2*3*4*5*6*7*8*9))
  		cout<<a<<" "<<b<<" "<<c<<endl;
  	}
  
  	return 0;
  }
  ```

  

## P3853 [TJOI2007]路标设置

- 二分答案

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e5+10;
  int sign[N];
  int l,n,k,ans;
  bool check(int x)
  {
  	int sum=0,now=0;//sum记录新增路牌数量，now记录当前左端点
  	for(int i=1;i<=n;i++)
  	{
  		if(sum>k) //这个要加，不然有个测试点会TLE
  		return false;
  		if(sign[i]-now>x)
  		{
  			now+=x;
  			sum++;
  			i--; //固定右端点
  		}
  		else
  		{
  			now=sign[i];
  		}
  	}
  	
  	if(sum<=k) return true;
  	else
  	return false;
  }
  int main()
  {
  	cin>>l>>n>>k;
  	
  	for(int i=1;i<=n;i++)
  	{
  		cin>>sign[i];
  	}
  	
  	int left=0,right=l;
  	while(left<=right)
  	{
  		int mid=left+(right-left)/2;
  		if(check(mid))
  		{
  			ans=mid;
  			right=mid-1;
  		}
  		else
  		left=mid+1;
  	}
  	cout<<ans;
  	return 0;
   } 
  ```

  

























































