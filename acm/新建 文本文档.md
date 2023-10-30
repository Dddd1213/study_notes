# Week 11

## P1796 汤姆斯的天堂梦

-  思路：dp

  ```
  #include<iostream>
  using namespace std;
  int n,n_i,id,c;
  int f[101][101];
  int main(){
  	cin>>n; 
  	for(int i=1;i<=n;i++){
  		cin>>n_i;
  		for(int j=1;j<=n_i;j++){
  			cin>>id;
  			f[i][j]=2147483647; 
  			while(id!=0){
  				cin>>c;
  				f[i][j]=min(f[i-1][id]+c,f[i][j]);
  				cin>>id;
  			} 
  		}
  	}
  	int min_c=0x7fffffff;
  	for(int i=1;i<=n_i;i++){
  		min_c=min(f[n][i],min_c);
  	}
  	cout<<min_c;
  	return 0;
  } 
  ```

  

## P1806 跑步

-  思路：动态规划

```
#include<iostream>
using namespace std;
typedef long long ll;
ll n,ans[501];
int main(){
	cin>>n;
	ans[0]=1;
	for(int i=1;i<=n;i++){
		for(int j=n;j>=i;j--){
			ans[j]+=ans[j-i];
		}
	}
	cout<<ans[n]-1;
	return 0;
}
```



## P8742 [蓝桥杯 2021 省 AB] 砝码称重

```
#include<bits/stdc++.h>
using namespace std;
const int max1=150000;
int n,sum,w;
bool dp[2][300010];
int main(){
	cin>>n;
	dp[0][max1]=1;
	for(int i=1;i<=n;++i) {
		cin>>w;
		sum += w;
		for(int j=-sum; j<=sum;++j)
			dp[i&1][j+max1] = dp[i-1&1][j-w+max1]||dp[i-1&1][j+w+max1]||dp[i-1&1][j+max1];
	}
	int ans=0;
	for(int i=1;i<=sum;++i) ans += dp[n&1][i+max1];
	printf("%d\n",ans);
	return 0;
}
```



## P1959 遗址

-  找到正方形的四个顶点，用两个点确定该正方形即可。

  ```
  #include<bits/stdc++.h>
  #define max(a,b) a>b?a:b
  using namespace std;
  bool dp[5020][5020];
  int x[3020],y[3020],n,m;
  int main(){
  	cin>>n;
  	for(int i=1;i<=n;i++){
          scanf("%d%d",x+i,y+i);
          dp[x[i]][y[i]]=1;
  	}
  	int ans=0;
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			int a=x[i]-x[j];
  			int b=y[i]-y[j];
  			int Ax=x[i]-b;
  			int Bx=x[j]-b;
  			int Ay=y[i]+a;
  			int By=y[j]+a;
              if(Ax<1||Ax>5000||Bx<1||Bx>5000||Ay<1||Ay>5000||By<1||By>5000)continue;
  			if(dp[Ax][Ay] && dp[Bx][By])ans=max(ans,a*a+b*b);
  		}
  	}
  	printf("%d\n",ans);
      return 0;
  }
  ```

  

## P8794 [蓝桥杯 2022 国 A] 环境治理

- 思路：最短路径问题，Floyd模板+二分查找

  ```
  #include<bits/stdc++.h>
  #define int long long
  using namespace std;
  int x[101][101],dis[101][101],diss[101][101], n,m;
  int cheak(int k){
  	int p=k/n;
  	int o=k%n;
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			diss[i][j]=max(dis[i][j]-p,x[i][j]);
  			if(o >= i)diss[i][j]=max(diss[i][j]-1,x[i][j]);
  		}
  	}
  	int ans=0;
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			for(int k=1;k<=n;k++){
  				diss[j][k]=min(diss[j][k],diss[j][i]+diss[i][k]);
  			}
  		}
  	}
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			ans+=diss[i][j];
  	}
  	}
  	if(ans<=m)return 1;
  	return 0;
  }
  inline int read(){
  	int x=0,f=1;
  	char ch=getchar();
  	while (ch<'0'||ch>'9'){
              if (ch=='-') f=-1;
              ch=getchar();
      }
  	while (ch>='0'&&ch<='9'){
              x=(x<<3)+(x<<1)+ch-48;
              ch=getchar();
      }
  	return x*f;
  }
   
  signed main(){
  	n=read();
  	m=read();
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			dis[i][j]=read();
  		}
  	}
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			x[i][j]=read();
  		}
  	}
  	int p=0;
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			for(int k=1;k<=n;k++){
  				dis[j][k]=min(dis[j][k],dis[j][i]+dis[i][k]);
  			}
  		}
  	}
  	for(int i=1;i<=n;i++){
  		for(int j=1;j<=n;j++){
  			p += dis[i][j];
  		}
  	}
  	if(p<=m){
  		cout<<"0";
  		return 0;
  	}
  	int l=1,r=1e6;
  	while(l<=r){
  		int mid=(l+r)>>1;
  		if(cheak(mid)==1)r=mid-1;
  		else l=mid+1;
  	}
  	if(l==1e6+1){
  		cout<<"-1";
  		return 0;
  	}
  	cout<<l;
  	return 0;
  }
  ```

  















