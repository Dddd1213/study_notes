# Week 6 贪心算法

## P1199 [NOIP2010 普及组] 三国游戏

-  知识点：贪心、博弈题

-  思路：计算机的选择是贪心的，所以玩家和计算机都不可能选到默契值最大的武将组合。

  小涵赢的情况：在第二次选将时可以拿到第一次选中的武将的次大默契值组合。

```c++
#include<bits/stdc++.h>
using namespace std;
int a[501][501],n;
int main()
{
	int m=0;
    cin>>n;
    for(int i=1;i<=n;i++) {
        for(int j=i+1;j<=n;j++) {
            cin>>a[i][j];
            a[j][i]=a[i][j];
        }
    }
    for(int i=1;i<=n;i++)
    {
        sort(a[i]+1,a[i]+n+1);
        if(a[i][n-1] > m) 
        m= a[i][n-1];
    }
    cout<<"1"<<endl<<m;
    return 0;
}
```



## P1007 独木桥

- 思路：和之前写过的蚂蚁很像，两士兵碰头调转可以看成直接穿过士兵往前走（因为每个士兵没有差别 

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  int main()
  {
  	int l,n,ma=0,mi=0;
  	cin>>l>>n;
  	while(n--)
  	{
  		int x;
  		cin>>x;
  		ma=max(ma,max(x,l+1-x));
  		mi=max(mi,min(x,l+1-x));
  	}
  	cout<<mi<<" "<<ma<<endl;
  	return 0;
  }
  ```

  

## P1223 排队接水

-  知识点：贪心算法

-  思路：数组从小到大排序~

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1005;
  struct sz{
  	int t,num;
  };
  bool cmp(sz x, sz y)
  {
  	return x.t <y.t;
  }
  int main()
  {
  	int n;
  	double sum=0;
  	sz a[N];
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		a[i].num =i;
  		cin>>a[i].t;
  	}
  	sort(a+1,a+n+1,cmp);
  	for(int i=1;i<=n;i++)
  	cout<<a[i].num <<" ";
  	cout<<endl;
  	for(int i=1;i<=n;i++)
  	{
  		int j=n-i;
  		sum+=a[i].t *j;
  	}
  	printf("%.2f",sum/n);
  	return 0;
  }
  ```

  

## P1090 [NOIP2004 提高组] 合并果子 

- 考点：贪心+优先队列
-  思路：每次合并最小的两堆即可

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int n,ans=0;
	priority_queue<int,vector<int>,greater<int>> a;
	cin>>n;
	while(n--)
	{
		int x;
		cin>>x;
		a.push(x);
	}
	while(1)
	{
		int x=a.top();
		a.pop();
		if(a.empty())
		break;
		x+=a.top();
		a.pop();
		ans+=x;
		a.push(x);
	}
	cout<<ans<<endl;
	return 0;
}
```

