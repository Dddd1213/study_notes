# 第一次双周赛+Week 4

## 第一次双周赛

### 7-2 a*b

- 知识点：十六进制数的高精度乘法

- 核心代码：用两个for循环处理

  ```
  for(int i=0;i<len1;i++)
  	{
  		len3=i;
  		for(int j=0;j<len2;j++)
  		{
  			z[len3]+=x[i]*y[j];
  			if(z[len3]>=16)
  			{
  				z[len3+1]+=z[len3]/16;
  				z[len3]%=16;
  			}
  			len3++;
  		}
  	}
  ```

-  注意：要记得去掉先导0！！

  ```
  int index=len1+len2;
  	while(index>0&&z[index]==0)
  	index--;
  	for(int i=index;i>=0;i--)
  	{
  		if(z[i]>=0&&z[i]<=9)
  		cout<<z[i];
  		else 
  		cout<<q[z[i]-10];
  	}
  ```

### 7-3 山头狙击战

- 知识点：二分答案（装子弹时间：最大的最小

-  主要思路：mi[]数组记录敌人一开始的位置，sort排序，从离小明最近的敌人开始考虑

  ​					用s记录下一个敌人往前走了多少

  ​					mi[i]-s表示敌人距离小明的相对位置

  ​					二分法检查当前答案是否能让能击倒敌人数大于实际敌人数

  ```
  #include<bits/stdc++.h>
  using namespace std;
  int m,n,mi[10000005],ans=-1,sum=0,s=0;
  bool check(int k)
  {
  	if(mi[0]>m)
  	s=mi[0]-m; //s表示下一个敌人往前走了多少 
  	sum++;
  	for(int i=1;i<n;i++)
  	{
  		s+=k;
  		if(mi[i]<s) break; //敌人已经到小明处
  		else if(mi[i]-s>m) //敌人在小明射程以外
  		{
  			s+=mi[i]-s-m;
  			sum++; 
  		 } 
  		 else
  		 sum++;
  	}
  	
  	if(sum>=n) return true;
  	return false;
  }
  int main()
  {
  	cin>>n>>m;
  	for(int i=0;i<n;i++)
  	cin>>mi[i];
  	sort(mi,mi+n);
  	
  	int l=1,r=mi[n-1];
  	while(l<=r)
  	{
  		int mid=l+(r-l)/2;
  		if(check(mid))
  		{
  			ans=mid;
  			l=mid+1;
  		}
  		else
  		{
  			r=mid-1;
  		}
  		sum=0;
  		s=0;
  	}
  	cout<<ans<<endl;
  	return 0;
  }
  ```

### 7-4 Reversing Linked List



### 7-5 一元三次方程



## Week4

### 知识点小结

- #### BFS：广度优先搜索

  - 思路 ：

    1. 将起点推入队列中 

    2.  将起点标识为已走过

       while（队列非空）{

       3. 取队列首结点vt，并从队列中弹出

       4. 探索上面取出的结点周围是否有没走过的结点vf

          若有：将所有能走的vf的parents指向vt，并将vt加入队列

          （如果vf等于终点，说明探索完成，退出循环）

       }

       如果队列为空则自然跳出，说明无路可达终点

  -  典型题型

- ####  DFS：深度优先搜索

  -  思路：

    ```
    void DFS(当前节点){
        对当前节点的访问;`在这里插入代码片`
        标记当前节点为已访问;
        for(下一节点 : 当前节点的邻接列表){
            剪枝(如果下一节点已经访问过就跳过);
            DFS(下一节点);
        }
    }
    ```

  -  典型题型：寻找连通块

### 题

#### P1605 迷宫

- 知识点：dfs深度优先搜索 

- 注意：题目中给的下标是从1开始的不是从0开始的！！

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  int n,m,t,sum=0;
  int sx,sy,fx,fy;
  int mp[100][100]={0};//1表示障碍，0表示路 
  int vis[100][100]={0};//1表示走过，0表示没到过 
  int cx[5]={-1,0,1,0};
  int cy[5]={0,1,0,-1}; 
  void dfs(int x,int y)
  {
  	if(x==fx&&y==fy)
  	{
  		sum++;
  		return;
  	}
  	else
  	{
  		for(int i=0;i<4;i++)
  		{
  			int nx=x+cx[i];
  			int ny=y+cy[i];
  			if(nx>0&&nx<=m&&ny>0&&ny<=n&&vis[nx][ny]==0&&mp[nx][ny]==0)
  			{
  				vis[nx][ny]=1;
  				dfs(nx,ny);
  				vis[nx][ny]=0;
  			}
  		}
  	}
  }
  int main()
  {
  	cin>>n>>m>>t;
  	cin>>sx>>sy>>fx>>fy;
  	while(t--)
  	{
  		int zx,zy;
  		cin>>zx>>zy;
  		mp[zx][zy]=1;
  	 } 
  	 vis[sx][sy]=1;
  	 dfs(sx,sy);
  	 cout<<sum<<endl;
  	return 0;
  }
  ```

#### P1443 马的遍历

-  知识点：bfs广度优先搜索

- 注意：

  - 马需要按“日”字形走
  - （代码块1是不知道哪错了的错误答案

  ```
  #include<bits/stdc++.h>
  using namespace std;
  int n,m,x,y;
  queue<pair<int,int> > q;
  int mp[500][500]={0};
  int vis[500][500]={0};
  
  int dx[8]={-2,-2,-1,-1,1,1,2,2};
  int dy[8]={-1,1,-2,2,-2,2,-1,1};
  
  void bfs()
  {
  	while(!q.empty())
  	{
  		int nx=q.front().first;
  		int ny=q.front().second;
  		q.pop();
  		for(int i=0;i<8;i++)
  		{
  			int xx=nx+dx[i];
  			int yy=ny+dy[i];
  			if(xx>=1&&xx<=n&&yy>=1&&yy<=n&&vis[xx][yy]==0)
  			{
  				vis[xx][yy]=1;
  				mp[xx][yy]=mp[nx][ny]+1;
  				q.push(make_pair(xx,yy));
  			}
  		}
  	}
  }
  int main()
  {
  	cin>>n>>m>>x>>y;
  	q.push(make_pair(x,y));
  	vis[x][y]=1;
  	mp[x][y]=0;
  	bfs();
  	for(int i=1;i<=n;i++)
  	{
  		for(int j=1;j<=m;j++)
  		{
  			if((i!=x||j!=y)&&mp[i][j]==0) cout<<"-1 ";
  			else 
  			cout<<mp[i][j]<<" ";
  		}
  		cout<<endl;
  	}
  	return 0;
  }
  ```

  ```
  #include<bits/stdc++.h>
  using namespace std;
  int n,m,x,y,xx,yy,sx,sy,b[405][405],ans[405][405],dx[8]={-2,-2,-1,-1,1,1,2,2},dy[8]={-1,1,-2,2,-2,2,-1,1};
  deque<pair<int,int>> num;
  void bfs(){
  	while (!num.empty()){
  		x=num.begin()->first;
  		y=num.begin()->second;
  		num.pop_front();
  		for (int i=0;i<8;i++){
  			xx=x+dx[i],yy=y+dy[i];
  			if (xx>=1&&yy>=1&&xx<=n&&yy<=m&&b[xx][yy]==0){
  				b[xx][yy]=1;
  				if (ans[xx][yy]<ans[x][y]+1) ans[xx][yy]=ans[x][y]+1;
  				num.push_back(make_pair(xx,yy));
  			}
  		}
  	}
  }
  int main(){
  	cin.tie(0);
  	ios::sync_with_stdio(false);
  	cin>>n>>m>>sx>>sy;
  	num.push_back(make_pair(sx,sy)),b[x][y]=1;
  	bfs();
  	ans[sx][sy]=0;
  	for (int i=1;i<=n;i++){
  		for (int j=1;j<=m;j++){
  			if ((i!=sx||j!=sy)&&ans[i][j]==0) printf("-1   ");
  			else printf("%-5d",ans[i][j]);
  		}
  		printf("\n");
  	}
  	return 0;
  }
  ```

  

