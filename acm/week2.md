# week 2 二分法应用

### P2249 【深基13.例1】查找

-  二分查找函数：lower_bound()

-  快读：原理：读入字符比数字快

  ```
  #include<bits/stdc++.h>
  using namespace std;
  const int MAXN=1e6+10;
  int read()//快读
  {
  	int x=0,f=1;//f用来记录正负号 
  	char c=getchar();
  	while(c<'0'||c>'9'){
  		if(c=='-') f=-1;
  		c=getchar();
  	}
  	while(c>='0'&&c<='9'){
  		x=x*10+c-'0';
  		c=getchar();
  	}
  	return x*f;
  }
  int a[MAXN];
  int main()
  {
  	int n=read(),m=read();
  	for(int i=0;i<n;i++) a[i]=read();
  	while(m--)
  	{
  		int b=read();
  		int x=lower_bound(a,a+n,b)-a;
  //		cout<<"x="<<x<<endl;
  		if(a[x]==b) printf("%d ",x+1); 
  		else printf("-1 ");
  	}
  	
  	return 0;
  }
  ```

  

### P1824 进击的奶牛

- 二分答案

  - 前提：答案在一个区间内；直接搜不好搜，但容易判断一个答案可不可行；该区间相对题目具有单调性。
  - 特征：最大的最小，最小的最大

-  注意check函数

-  思路：0到最远牛棚间取一半，判断当前距离代入能否装下所有的牛，然后二分法找出最大值（这题去年寒假没看懂没想到在这又遇到了QAQ）

  原题链接：https://www.luogu.com.cn/problem/P1824

  ```
  #include<bits/stdc++.h>
  using namespace std;
  int n,c,a[100010];
  int r=1e9,l=0,ans=0;
  bool check(int mid)
  {
  	int sum=1;
  	int now=a[1];
  	for(int i=1;i<=n;i++)
  	{
  		if(a[i]-now>=mid)
  		{
  			sum++;
  			now=a[i];
  			}	
  		if(sum>=c)
  		return true;
  	}
  	return false;
  }
  int main()
  {
  	cin>>n>>c;
  	for(int i=1;i<=n;i++)
  	cin>>a[i];
  	sort(a+1,a+1+n);
  	for(int i=1;i<n;i++)
  	l=min(a[i+1]-a[i],l);
  	r=a[n]-a[1];
  	
  	while(l<=r)
  	{
  		int mid=l+(r-l)/2;
  		if(!check(mid)) r=mid-1;
  		else
  		{
  			ans=mid;
  			l=mid+1;
  		}
  	}
  	cout<<ans<<endl;
  	return 0;
   } 
  ```

  

### P2678 [NOIP2015 提高组] 跳石头

-  也是二分查找！

- check部分：如果间隙小于mid，就说明要搬走石头。最后看要搬走的石头数量和最多能搬走的石头数比较即可。

  ```
  #include<bits/stdc++.h>
  using namespace std;
  int l,m,n,a[200010],ans=-1;
  bool check(int mid)
  {
  	int now=0,count=0;
  	for(int i=1;i<=n+1;i++)
  	{
  		if(a[i]-now<mid)
  		count++;
  		else
  		now=a[i];
  	}
  	if(count>m)
  	return false;
  	return true;
  }
  
  int main()
  {
  	cin>>l>>n>>m;
  	a[0]=0;
  	for(int i=1;i<=n;i++)
  	cin>>a[i];
  	a[n+1]=l;
  	int left=1,right=l;
  	
  	while(left<=right)
  	{
  		int mid=left+(right-left)/2;
  		if(check(mid))
  		{
  			ans=mid;
  			left=mid+1;
  		}
  		else
  		right=mid-1;
  	}
  	cout<<ans<<endl;
  	return 0;
  }
  ```

  