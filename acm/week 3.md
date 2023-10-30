# Week 3 线性表

## P3613 【深基15.例2】寄包柜

- 分析：此题直接用数组得1e9个格子，内存要爆

- 知识点

  - map容器：

    find()   返回数据所在位置的迭代器

    insert()   插入pair

  - 迭代器：iterator，用于遍历容器元素（类似指针），输出*迭代器打印元素的值

- 主要思路：vector容器嵌套map容器

  柜子数i当做vector的下标

  寄包的格子数j当做map的key值

  寄存的物品k当做map的val值

-  注意：map的特点是不能出现重复key值，当有重复插入时会忽略而不是覆盖，所以要多加一个判断find(key) ，若已有，则调用erase函数将原key删除再插入新的

```
#include<bits/stdc++.h>
using namespace std;
int main()
{
	int n,q,num,a,key,val; 
	cin>>n>>q;
	vector<map<int,int>> v;
	vector<int> s;
	//创建n个柜子 
	for(int i=0;i<n;i++)
	{
		map<int,int> mymap;
		v.push_back(mymap);
	}
	while(q--)
	{
		map<int,int>::iterator pos; //迭代器
		cin>>num;
		if(num==1)
		{
			cin>>a>>key>>val;
			pos=v[a-1].find(key);
			if(pos!=v[a-1].end())
			v[a-1].erase(pos);
			v[a-1].insert(pair<int,int>(key,val));
		 } 
		 else
		 {
		 	cin>>a>>key;
		 	s.push_back(v[a-1][key]);
		 }
	 } 
	 for(int i=0;i<s.size();i++)
	 cout<<s[i]<<endl;
	return 0;
}
```



## P1241 括号序列

- 一开始试图用两个栈ab存左右括号，试了半天没存明白，，，然后找到了更方便的题解，大佬还是大佬orz

- 主要思路：是先找右括号，在往前找未被匹配的第一个左括号。

  用数组a标记该字符是否被匹配过，若匹配过则直接输出，未匹配就成对输出

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  int main()
  {
  	int a[105]={0};
  	string s;
  	cin>>s;
  	int len=s.length();
  	for(int i=0;i<len;i++)
  	{
  		if(s[i]==')')
  		{
  			for(int j=i;j>=0;j--) //这里不能直接j=i-1吧，i=0时可能会出错
  			{
  				if(s[j]=='('&&a[j]==0) 
  				{
  					a[i]=1;
  					a[j]=1;
  					break;
  				}
  				if(s[j]=='['&&a[j]==0)
  				break;
  			 } 
  		}
  		if(s[i]==']')
  		{
  			for(int j=i;j>=0;j--)
  			{
  				if(s[j]=='['&&a[j]==0) 
  				{
  					a[i]=1;
  					a[j]=1;
  					break;
  				}
  				if(s[j]=='('&&a[j]==0)
  				break;
  			 } 
  		}
  		}
  			for(int i=0;i<len;i++)
  		{
  			if(a[i]==0)
  			{
  				if(s[i]=='('||s[i]==')') cout<<"()";
  				else cout<<"[]"; 
  			}
  			else
  			cout<<s[i];
  	}
  	return 0;
  }
  ```

  

## P1449 后缀表达式

- 终于写到会做的题了喜大普奔感天动地可爱的后缀表达式

- 知识点：队列的应用

- 主要思路：遇到数字放进去，遇到运算符取栈顶两个数字，注意运算左右别错了！

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  int main()
  {
  	stack<int>s;
  	char ch;
  	int a=0;
  	while(cin>>ch)
  	{
  		if(ch=='@')
  		break;
  		//如果是数字 
  		else if(ch<='9'&&ch>='0')
  		{
  			a=a*10+ch-'0';
  		}
  		else if(ch=='.')
  		{
  			s.push(a);
  			a=0;
  		}
  		else
  		{
  			int a1=s.top();
  			s.pop();
  			int b1=s.top();
  			s.pop();
  			if(ch=='+') s.push(a1+b1);
  			if(ch=='-') s.push(b1-a1);
  			if(ch=='*') s.push(a1*b1);
  			if(ch=='/') s.push(b1/a1);
  		}
  	}
  	cout<<s.top()<<endl;
  	return 0;
   } 
  ```



## P1160 队列安排

-  自己做出来但没完全做出来的一道....超时我真的恨

-  虽然但是也掌握了vector一些用法8

  ```c++
  #include<bits/stdc++.h>
  using namespace std;
  int main()
  {
  	int n,m;
  	vector<int> v;
  	v.push_back(1);
  	cin>>n;
  	for(int i=2;i<=n;i++)
  	{
  		int k,p;
  		cin>>k>>p;
  		if(p==0)
  		{
  			 vector<int>::iterator it = find(v.begin(), v.end(), k);
  			 if(it!=v.end())
  			 v.insert(it,i);
  		}
  		else
  		{
  			 vector<int>::iterator it = find(v.begin(), v.end(), k);
  			 if(it!=v.end())
  			 v.insert(it+1,i);
  		}
  	}
  
  	cin>>m;
  	while(m--)
  	{
  		int x;
  		cin>>x;
  	    vector<int>::iterator it = find(v.begin(), v.end(), x);
  		if(it!=v.end())
  		v.erase(it);
  	}
  	for(vector<int>::iterator it=v.begin();it!=v.end();it++)
  	{
  		cout<<*it<<" ";
  	}
  	return 0;
   } 
  ```

看看正解：

-  知识点：list的应用

  - list的底层是双向链表结构

  - 优点：与其他的序列式容器相比(array，vector，deque)，list在任意位置进行插入、移除元素的执行效率更好。
  - 缺点：不支持任意位置的随机访问，比如：要访问list的第6个元素，必须从已知的位置(比如头部或者尾部)迭代到该位置。

```c++
#include<bits/stdc++.h>
using namespace std;

list<int>::iterator pos[100010];
//pos[x] 表示x的迭代器
list<int> L;
bool vis[100010];
int N, k, p, M, x;

int main()
{
    cin >> N;
    L.push_front(1);
    //默认1迭代器地址为begin
    pos[1] = L.begin();
    for(int i = 2; i <= N; i++)
    {
        cin >> k >> p;
        //insert返回值也为迭代器
        if(p == 0) pos[i] = L.insert(pos[k], i);
        else
        {
            //或者 auto nex = next(pos[k]);
            //往k的右边插入，要找到k的下一个迭代器，用next查找
            list<int>::iterator nex = next(pos[k]);
            pos[i] = L.insert(nex, i);
        }
    }
    cin >> M;
    for(int i = 0; i < M; i++)
    {
        cin >> x;
        if(!vis[x]) L.erase(pos[x]), vis[x] = true;
    }
    for(list<int>::iterator it = L.begin(); it != L.end(); it++) cout << *it <<" ";
    //或者 for(auto X : L) cout << X <<" ";
    return 0;
}
```

