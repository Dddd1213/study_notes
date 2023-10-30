# RoboCom 本科组初赛

[TOC]

## 7-1 懂的都懂

- 思路：暴力把原图所有可能出现的和都算一遍存下来

- 疑问：（参考部分和问题）如果用深搜，判断每一个拿或不拿复杂度应该是2^n？

- 答：题目已经定了只用选4个数，部分和问题中用深搜是选择若干个数，所以时间复杂度高

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int n,k;
  int yuan[100],sum[20000],xin[300];
  
  int main()
  {
  	cin>>n>>k;
  	
  	for(int i=0;i<n;i++)
  	cin>>yuan[i];
  	
  	for(int a=0;a<n;a++)
  	{
  		for(int b=a+1;b<n;b++)
  		{
  			for(int c=b+1;c<n;c++)
  			{
  				for(int d=c+1;d<n;d++)
  				{
  					sum[yuan[a]+yuan[b]+yuan[c]+yuan[d]]=1;
  				}
  			}
  		}
  	}
  	
  	while(k--)
  	{
  		bool f=1;
  		int kk;
  		cin>>kk;
  		for(int i=0;i<kk;i++)
  		cin>>xin[i];
  		for(int i=0;i<kk;i++)
  		{
  			if(!sum[xin[i]*4])
  			{
  				f=0;
  				break;
  			}
  		}
  		if(f)
  		cout<<"Yes"<<endl;
  		else
  		cout<<"No"<<endl;
  	}
  	
  	return 0;
   } 
  ```

  

## 7-2 芬兰木棋

- 思路：（因为vector下标不能为负）利用mp将斜率离散化，然后用vector存下来，再根据离原点距离排序。由近到远，如果score为1，就能攒着一起打，如果大于1，单独打分更多。

- 注意：第一三象限可能斜率一样，但是方向不一样（二四象限同理），所以再用个mp2把y为正和y为负的情况区分开。

- 错解：

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e5+10;
  int n,cnt=10,ans,sum;
  map<double,int>mp1,mp2;
  struct muqi{
  	double x;
  	int score;
  }mq[N]; 
  
  bool cmp(muqi a,muqi b)
  {
  	return abs(a.x)<abs(b.x);
  }
  
  int main()
  {
  	cin>>n;
  	vector<muqi> v[70000];
  	for(int i=0;i<n;i++)
  	{
  		double y;
  		cin>>mq[i].x>>y>>mq[i].score;
  		double xl= y /mq[i].x;
  		if(y>0)
  		{
  			if(mp1[xl]<1)
  			mp1[xl]=cnt++;
  			v[mp1[xl]].push_back(mq[i]);
  		}
  		else
  		{
  			if(mp2[xl]<1)
  			mp2[xl]=cnt++;
  			v[mp2[xl]].push_back(mq[i]);
  		}
  	}
  	
  //	cout<<endl;
  	
  	for(int i=10;i<cnt;i++)
  	{
  		sort(v[i].begin(),v[i].end(),cmp); 
  		int js=0;
  		for(auto it:v[i])
  		{
  //			cout<<it.x<<" "<<" "<<it.score<<endl;
  			if(it.score==1)
  			{
  				js++;
  				continue;
  			}
  			else
  			{
  				sum+=js;
  				if(js)
  				ans++;
  				sum+=it.score;
  				js=0;
  				ans++;
  			}
  		}
  //		cout<<endl;
  	}
  	
  	cout<<sum<<" "<<ans<<endl;
  	return 0;
  }
  ```

- 更正：vector里不用存所有数据，存mq[N]的下标就可以！！！然后对着下标再到mq数组里找出对应的分数！另外，在main外面定义vector好像能开得更大一点。

- 更正2：不用计算斜率（可能会有误差），用map<pii,int>存，将横纵坐标gcd后make_pair存进去

- 更正3：不能用x判断谁离原点近，如果有一条直线在y轴上（x全为0）就判断不了

  ```C++
  #include<bits/stdc++.h>
  #define int long long
  using namespace std;
  typedef pair<int,int> pii;
  const int N=1e5+10;
  int n,cnt=10,ans,sum;
  map<pii,int>mp1,mp2;
  vector<int> v[N];
  struct muqi{
  	int x;
  	int score;
  }mq[N]; 
  
  bool cmp(int a,int b)
  {
  	return abs(mq[a].x)<abs(mq[b].x);
  }
  
  signed main()
  {
  	cin>>n;
  
  	for(int i=0;i<n;i++)
  	{
  		int y;
  		cin>>mq[i].x>>y>>mq[i].score;
  		sum+=mq[i].score;
  		int gcd=abs(__gcd(mq[i].x,y)); //gcd这里要加绝对值，不然可能为负 
  		mq[i].x /=gcd;
  		y/=gcd;
  		if(y>0)
  		{
  			if(mp1[make_pair(mq[i].x ,y)]<1)
  			mp1[make_pair(mq[i].x ,y)]=cnt++;
  			v[mp1[make_pair(mq[i].x ,y)]].push_back(i);
  		}
  		else
  		{
  			if(mp2[make_pair(mq[i].x ,y)]<1)
  			mp2[make_pair(mq[i].x ,y)]=cnt++;
  			v[mp2[make_pair(mq[i].x ,y)]].push_back(i);
  		}
  		mq[i].x=gcd; //不能用x判断谁离原点近，如果有一条直线在y轴上就判断不了了 
  	}
  	
  //	cout<<endl;
  	
  	for(int i=10;i<cnt;i++)
  	{
  		sort(v[i].begin(),v[i].end(),cmp); 
  		int js=0;
  		bool st=0;
  		for(auto it:v[i])
  		{
  			if(mq[it].score!=1)
  			{
  				st=0;
  				ans++;
  			}
  			else
  			{
  				if(!st)
  				{
  					st=1;
  					ans++;
  				}
  			}
  //如果最后一个是1，就没走else那部分，ans不变，出错 
  //			if(mq[it].score==1)
  //			{
  //				js++;
  //			}
  //			else
  //			{
  //				if(js)
  //				ans++;
  //				js=0;
  //				ans++;
  //			}
  		}
  	}
  	
  	cout<<sum<<" "<<ans<<endl;
  	return 0;
  }
  ```

  

































