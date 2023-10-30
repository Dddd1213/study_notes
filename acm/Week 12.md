# Week 12

## P1776 宝物筛选

- 思路：多重背包问题，用二进制优化

- 注意：看清题目输入的顺序

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+10;
  int w[N],v[N],s[N],dp[N];
  int main()
  {
  	int W,n;
  	cin>>n>>W;
  	
  	int num=1;
  	for(int i=1;i<=n;i++)
  	{
  		int x,y,z;
  		cin>>y>>x>>z;
  		for(int j=1;j<=z;j<<=1)
  		{
  			w[num]=j*x;
  			v[num++]=j*y;
  			z-=j;
  		}
  		if(z)
  		{
  			w[num]=z*x;
  			v[num++]=z*y;
  		}
  	}
  	
  	for(int i=1;i<num;i++)
  	{
  		for(int j=W;j>=w[i];j--)
  		{
  			dp[j]=max(dp[j],dp[j-w[i]]+v[i]);
  		}
  	}
  			
  	cout<<dp[W]<<endl;
  	
  	return 0;
   } 
  ```

  

## P1555 尴尬的数字

- 思路：因为二进制只有0和1，错误比较好判断，所以直接for循环遍历每一位出错的情况，转化为十进制（n），再转化为三进制（s3）。

- 特判：对于s1开头位是0的情况，可以直接得知该位错误，输出十进制。

- 注意：string按位赋值初始化时，不能使用s2[i]=s1[i]，要用s2+=s1[i]。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int zh2(string s)
  {
  	int len=s.length();
  	int n=0;
  	int k=1; //这个k记得放for循环外面啊啊！！找了半天 
  	for(int i=len-1;i>=0;i--)
  	{
  		if(s[i]=='1')
  		n+=k;
  		k=k*2;
  	}
  //	cout<<n<<endl;
  	return n;
  }
  
  string zh3(int n)
  {
  	string s,s2;
  	int i=0;
  	while(n>0)
  	{
  		s+=n%3+'0';
  		n=n/3;
  	}
  	int p=0;
  	int len=s.length();
  	for(int j=len-1;j>=0;j--)
  	s2=s2+s[j];
  //	cout<<s2<<endl;
  	return s2;
  }
  
  int main()
  {
  	string s1,s2,s3;
  	int n;
  	cin>>s1>>s2;
  	
  	//开头是0，特判
  	if(s1[0]=='0')
  	{
  		n=zh2(s1);
  		cout<<n<<endl;
  		return 0;
  	}
  		
  	int len1=s1.length();
  	for(int i=1;i<len1;i++)
  	{
  		if(s1[i]=='1') s1[i]='0';
  		else s1[i]='1';
  		
  		n=zh2(s1);
  		s3=zh3(n);
  		
  		int len3=s3.length();
  		int len2=s2.length(); 
  		int flag=0;
  		
  		if(len2==len3)
  		for(int j=0;j<len3;j++)
  		{
  			if(s2[j]!=s3[j])
  			flag++;
  		}
  		
  		if(flag==1)
  		{
  			cout<<n<<endl;
  			break;
  		}
  		
  		if(s1[i]=='1') s1[i]='0';
  		else s1[i]='1';
  	}
  	
  	return 0;
   } 
  ```

  

## P8845 [传智杯 #4 初赛] 小卡和质数

-  异或：先将数a b转化成二进制形式，按位比较，相同为0不同为1

-  思路：本题要求x y的异或值为1，即二进制表达形式只有最后一位不同，分别是0和1，即x y值只相差1。x y必定是一奇一偶，而2以外的偶数都能被2整除，不属于质数，所以只有当x y分别是1和2的时候满足题意。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int main()
  {
  	int t,x,y;
  	cin>>t;
  	while(t--)
  	{
  		cin>>x>>y;
  		if(x==1&&y==2) cout<<"Yes"<<endl;
  		else if(x==2&&y==1) cout<<"Yes"<<endl;
  		else cout<<"No"<<endl;
  	}
  	return 0;
  }
  ```

  















