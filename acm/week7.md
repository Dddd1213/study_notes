# Week 7 动态规划

## P1048 [NOIP2005 普及组] 采药

-  思路：跟背包问题的思路差不多，for循环遍历所有情况，把选该草药和不选该草药的价值情况比较大小，选大的。

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  const int N = 1005;
  int w[N],v[N];//草药的时间和价值 
  int res[N][N];//前i个草药中选择了总时间不超过j的草药 
  int main()
  {
  	int T,M;
  	cin>>T>>M;
  	for(int i=1;i<=M;i++)
  	cin>>w[i]>>v[i]; 
  	
  	for(int i=1;i<=M;i++)
  	{
  		for(int j=0;j<=T;j++)
  		{
  			if(w[i]>j)
  			res[i][j]=res[i-1][j];
  			else
  			res[i][j]=max(res[i-1][j],res[i-1][j-w[i]]+v[i]);
  		}
  	 } 
  	cout<<res[M][T]<<endl;
  	return 0;
   } 
  ```

  

## B3637 最长上升子序列

- 思路：之前百题有个导弹是要写最长非升子序列，当时还不会用动态规划、、

  ​			回到这题，b[i]表示的是以i结尾最长子序列的长度，每个数初始的b[i]都 			为1（本身），然后再定义一个j从1到i-1，比较a[i]和a[j]，比谁大就把			谁下面的数字拿过来再加1

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  int a[5005],b[5005];//b[i]表示以i结尾的最长子序列的长度 
  int main()
  {
  	int n,ans=0;
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	cin>>a[i];
  	
  	for(int i=1;i<=n;i++)
  	{
  		b[i]=1;
  		for(int j=1;j<i;j++)
  		{
  			if(a[j]<a[i])
  			b[i]=max(b[i],b[j]+1);
  		}
  		ans=max(ans,b[i]);
  	 } 
  	
  	cout<<ans<<endl;
  	return 0;
  }
  ```

  

## P1115 最大子段和

- 注意：ans的初值一定要赋小一点，，一开始赋值为0第二个测试点过不去

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int a[200005],b[200005];
  int main()
  {
  	int n,ans=-2147483647;
  	cin>>n;
  	for(int i=1;i<=n;i++)
  	{
  		cin>>a[i];
  		if(i<2) b[i]=a[i];
  		else
  		b[i]=max(a[i],b[i-1]+a[i]);
  		ans=max(ans,b[i]);
  	}
  	cout<<ans<<endl;
  	return 0;
  }
  ```

## LCS

- 思路：理不清了看这个吧[(275条消息) 动态规划解最长公共子序列(LCS)(附详细填表过程)_z-k的博客-CSDN博客_最长公共子序列动态规划算法](https://blog.csdn.net/weixin_40673608/article/details/84262695?ops_request_misc=%7B%22request%5Fid%22%3A%22167145266016800186554390%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=167145266016800186554390&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-3-84262695-null-null.142^v68^control,201^v4^add_ask,213^v2^t3_control2&utm_term=最长公共子序列&spm=1018.2226.3001.4187)

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
//没有其他编译器用了
using namespace std;
int dp[1010][1010];
char s1[100010], s2[100010], ans[1000010];//两个字符串以及路径

int main()
{
    scanf("%s%s", s1 + 1, s2 + 1);
	int len1 = strlen(s1 + 1), len2 = strlen(s2 + 1);
	for(int i = 1; i <= len1; i++)
	{
		for(int j = 1; j <= len2; j++)
		{
			dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
			if(s1[i] == s2[j])
            {
                dp[i][j] = max(dp[i][j], dp[i - 1][j - 1] + 1);
            }
		}
	}//LCS 板子
	int i = len1, j = len2;//简单的双指针
	while(dp[i][j] > 0)
    {
		if(s1[i] == s2[j])
        {
            ans[dp[i][j]] = s1[i];//反向追踪所有的字符
            i--, j--;
        }
		else
        {
			if(dp[i][j] == dp[i - 1][j]) i--;
			else j--;
		}
	}
	printf("%s", ans + 1);//printf YYDS!
    return 0;
}
```



















