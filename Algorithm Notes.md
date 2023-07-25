### Algorithm Notes

#### 1. 原数组是差分数组的前缀和

对于一维差分和二维查分都是如次。二维差分的实现？

所以，在差分数组中，增加一个元素，在原数组中，其右侧**（或者右下方）**的所有元素都增加这个值，如果要限定右边界，则需要右边界+1的位置减去这个元素。

##### 1.1 二维差分数组的初始化？

可以理解为插入一个1*1的小矩阵，左边界加，右边界+1减，和上述中的增加一个矩阵相同

另：求二维前缀和一定要更新前面的，后面的才正确

**二维矩阵insert函数：**（初始化则，x1,x2都为i，y1,y2都为j，相当于修改一个1*1小矩阵的大小）

``` 
auto insert=[&](int x1,int y1,int x2,int y2,LL c){
    b[x1][y1]+=c;
    b[x2+1][y1]-=c;
    b[x1][y2+1]-=c;
    b[x2+1][y2+1]+=c;
};
```



##### 三维前缀和

>  $S$ 是前缀和数组， $a$ 是原数组

 $S(x,y,z)=a(x,y,z)+S(x-1,y,z)+S(x,y-1,z)+S(x,y,z-1)-S(x-1,y-1,z)\\-S(x-1,y,z-1)-S(x,y-1,z-1)+S(x-1,y-1,z-1)$

观察规律可以发现，**每一个元素的正负号和减 $1$ 的奇偶个数相关**



##### 三维差分

>  $b$ 是差分数组，目的是为三维区间中的每一个数加上 $x$ 

获得差分数组：

```
b[x][y][z]+=x
b[x+1][y][z]-=x
b[x][y+1][z]-=x
b[x][y][z+1]-=x
b[x+1][y+1][z]+=x
b[x+1][y][z+1]+=x
b[x][y+1][z+1]+=x
b[x+1][y+1][z+1]-=x
```

观察规律：**正负号和 $+1$ 的奇偶数相关**



#### 2. 五种二分

##### 2.1 查找有无当前元素

``` 
int flag=-1;
while(l<=r){
    int mid=(l+r+1)/2;
    if(a[mid]<cnt)l=mid+1;
    else if(a[mid]>cnt)r=mid-1;
    else {
        flag=mid;
        break;
    }
}
if(flag==-1){
    cout<<"not found"<<endl;
}
else {
		cout<<"index:"<<flag<<endl;
}
```

##### 2.2 查找最后一个比当前元素小的元素

``` 
while(l<r){
    int mid=(l+r+1)/2;
    if(a[mid]<cnt)l=mid;//mid向下取整，l=mid可能使l一直保持一个数陷入死循环，所以mid要向上取整
    else r=mid-1;
}
cout<<"index:"<<l<<endl;//答案为l
```

##### 2.3 查找第一个比当前元素大的元素

``` 
while(l<r){
    int mid=(l+r)/2;
    if(a[mid]>cnt)r=mid;
    else l=mid+1;
}
cout<<"index:"<<r<<endl;//答案为r
```

##### 2.4 限制精度的二分

```
//寻找n的三次方根
while(r-l>=0.00000001) {
	double mid=(l+r)/2;
	if(calc(mid)>=n)r=mid;//calc计算mid的三次方
	else l=mid;
}
```

##### 2.5 三分

```
//求单峰函数的极值，eps控制精度
while(r-l>eps){
    double midl=l+(r-l)/3;
    double midr=l+(r-l)*2/3;
    if(calc(midl)<calc(midr)){
        l=midl;
    }
    else {
        r=midr;
    }
}
```



#### 3. 双指针

> 练过的题太简单了，有机会多练

1. **模版题**

[最长连续不重复子序列](https://www.acwing.com/problem/content/801/)

2. **另外一种双指针的思路**

[数组元素的目标和](https://www.acwing.com/problem/content/802/)



#### 4. 未知行数输入

**Java Scanner**

```
Scanner in = new Scanner(System.in);
String s;
while(true) {
	if((s=in.nextLine()).equals("")) {
		break;
	}
	System.out.println(s);
}

while(in.hasNextLine()) {
	if((s=in.nextLine()).equals(""))break;
	arr.add(Integer.parseInt(s));
}
```

**Java StringTokenizer**

```
Read in = new Read();
String s;
while(true) {
	s=in.nextLine();
	if(s==null)break;
	else out.println(s);
}
```

**C++**

```
string s;
while(getline(cin, s)){
    cout<<s<<endl;
}
```





#### 5. 字符串切割

cpp

``` 
s.substr(x,len);//表示从下标x开始切割长度为len的字符串
```

java

``` 
s.substring(x,y);//切割区间[x,y)，区间左开右闭
```



#### 6. 关于java的HashSet的哈希和查重问题

1. int、long等类型可以正确哈希和查重

2. String类型可以正确哈希和查重
3. 自己定义的类，如果要实现**根据元素的相同与否**查重，需要重写equals方法和hashCode方法，代码如下

``` 
static class Point{
	int x,y;
	Point(int x,int y){
		this.x = x;
		this.y = y;
	}
	public boolean equals(Object obj) {
		Point p = (Point) obj;
		return this.x==p.x&&this.y==p.y;
	}
	public int hashCode() {
		int code = this.x*5+this.y*7;
		return code;
	}
}
```



#### 7.并查集

注意并查集可以同时**支持查询连通块中点的数量**

注意：只有最高层节点有效，也就是查询点 $i$ 所处的连通块的节点数量，需要使用 $num[find(i)]$

``` 
int find(int x){
    if(x!=a[x]){
        a[x]=find(a[x]);
    }
    return a[x];
}
void unity(int x,int y){
    int xx=find(x);
    int yy=find(y);
    a[xx]=yy;
    num[yy]+=num[xx];//使用一个num数组存储该节点的连通块中点的数量，只有最高层节点有效
}
cout<<num[find(u)]<<endl;//输出点i所处的连通块的节点数量
```

**带权并查集：**[食物链]()



#### 8. 字符串hash

目的：将字串哈希成一个数字

方式：

1. 将字符串中的每个字符当作p进制数中的一个数
2. 将p进制数转换为10进制对应的值
3. 对 $mod$ 取余，再进行哈希

注意：

1. p取131或13331
2. mod取2^64-1，但超过unsigned范围自动取余，可以不用管

==重要式子： $h[r]-h[l-1]*p[r-l+1]$== //p[r-l+1]表示p^r-l+1^

**详细步骤：**

<img src="http://raw.githubusercontent.com/cys02/ImageRepository/master/IMG_3DB1F464F68A-1.jpeg" alt="IMG_3DB1F464F68A-1" style="zoom: 50%;" />

模版：[P3370 【模板】字符串哈希](https://www.luogu.com.cn/problem/P3370)

**C++代码**

```
typedef long long LL;
typedef unsigned long long ULL;

int P=131;//或者13331

char str[100005];
ULL h[100005];//用于记录前缀哈希值
ULL p[100005];//用户记录p的几次方

void solve(){
    string str;
    int n,m;
    cin>>n>>m;
    cin>>str;
    str=" "+str;
    p[0]=1;
    for(int i=1;i<=n;i++){
        p[i]=p[i-1]*P;
        h[i]=(h[i-1]*P+str[i]);
    }
    
    auto get=[&](int l,int r)->ULL{
        return h[r]-h[l-1]*p[r-l+1];
    };
    
    int l1,l2,r1,r2;
    for(int i=1;i<=m;i++){
        cin>>l1>>r1>>l2>>r2;
        if(get(l1,r1)==get(l2,r2))cout<<"Yes"<<endl;
        else cout<<"No"<<endl;
    }
}
```



**Java代码**

``` 
static long P=131;//哈希值，131或13331
static long[] p=new long[100005];//记录p的次方数
static long[] h=new long[100005];//记录前缀字符串的哈希值

static long get(int l,int r) {
	return h[r]-h[l-1]*p[r-l+1];//取得字串的哈希值
}

public static void main(String[] args) {
	PrintWriter out = new PrintWriter(System.out);
	Read in = new Read();
	int n = in.nextInt();
	int m = in.nextInt();
	String s=in.next();
	s=" "+s;
	char[] ch=s.toCharArray();
	p[0]=1;
	for(int i=1;i<=n;i++) {
		p[i]=p[i-1]*P;
		h[i]=h[i-1]*P+ch[i];
	}
	for(int i=1;i<=m;i++) {
		int l1=in.nextInt();
		int r1=in.nextInt();
		int l2=in.nextInt();
		int r2=in.nextInt();
		if(get(l1,r1)==get(l2,r2))out.println("Yes");
		else out.println("No");
	}
	out.flush();
}
```





#### 9. 单调栈问题

> 求==每个数==左边离它==最近的==且==比它小的==元素是什么？
>
> 如果==存下标==可以求每个数左边最近的比它小的元素的==位置==？（还没有试过）

性质：如果第 $i$ 个元素比第 $i+1$ 个元素大，则后续元素考虑 $a[i]$ 之前一定要先考虑 $a[i+1]$ ， $a[i]$ 就永远不会被取到。

所以，每来一个新的元素，while栈顶元素比当前元素大，则循环出栈，第一个比该元素小的元素即是该元素对应的答案。

**同时，该栈保持单调递增**

**C++**

```
void solve(){
    int n;
    cin>>n;
    int sta[n+1];
    int flag=-1;//栈从0开始，flag代表栈顶元素的下标
    for(int i=1;i<=n;i++){
        int cnt;
        cin>>cnt;
        while(flag>=0&&sta[flag]>=cnt)flag--;
        if(flag>=0)cout<<sta[flag]<<" ";
        else cout<<-1<<" ";
        sta[++flag]=cnt;
    }
    cout<<endl;
}
```

**Java**

```
public static void main(String[] args) {
	PrintWriter out = new PrintWriter(System.out);
	Read in = new Read();
	
	int n = in.nextInt();
	int[] sta=new int[n+1];
	int flag=-1;//栈从0开始，flag代表栈顶元素的下标
	for(int i=1;i<=n;i++) {
		int cnt = in.nextInt();
		while(flag>=0&&sta[flag]>=cnt)flag--;
		if(flag>=0)out.print(sta[flag]+" ");
		else out.print("-1 ");
		sta[++flag]=cnt;
	}
	out.println();
	out.flush();
}
```



#### 10. 单调队列

> 求滑动窗口中的最大值和最小值

重点：

1. ==队列中存储元素的下标==，如果**队头和当前到达的点距离超过滑动窗口的大小**，使队头出队
2. 和单调栈相似，队尾用于保证队列的单调递增
3. 对头用于：
    1. 控制滑动窗口的大小
    2. 保证队列单增——队头的下标即当前滑动窗口中最小的元素，输出的元素为对头下标对应的元素

==最大/最小值为队列左侧的指针代表的元素==，一直没有被换出去的元素总是最大/最小的，而左侧指针所代指的就是一直没被换出去的元素

**C++：输出大小为k的滑动窗口中的最大值和最小值**

```
void solve(){
    int n,k;
    cin>>n>>k;
    vector<int> a(n+1);
    for(int i=1;i<=n;i++)cin>>a[i];
    vector<int> up(n+1);
    int l=0,r=-1;//l和r表示队头和队尾，直接对应数组中队头和队尾的元素
    for(int i=1;i<=n;i++){
        //先利用队头，限制滑动窗口的大小——当前队头的下标不在当前滑动窗口中，则出队
        while(l<=r&&up[l]<i-k+1)l++;
        //模拟单调栈进栈
        while(l<=r&&a[up[r]]>=a[i])r--;
        //将当前元素的下标加入到队列中
        up[++r]=i;
        if(i>=k)cout<<a[up[l]]<<" \n"[i==n];
    }
    
    //求最大值
    vector<int> down(n+1);
    l=0;r=-1;
    for(int i=1;i<=n;i++){
        while(l<=r&&down[l]<i-k+1)l++;
        while(l<=r&&a[down[r]]<=a[i])r--;
        down[++r]=i;
        if(i>=k)cout<<a[down[l]]<<" \n"[i==n];
    }
}
```



#### 11. 读入空行

有些输入中可能含有空行

比如：<img src="http://raw.githubusercontent.com/cys02/ImageRepository/master/image-20230306172718370.png" alt="image-20230306172718370" style="zoom: 33%;" />

处理方式：

**1. C++**

``` 
getline(cin, s);//读入空行到s
```

**2. Java**

```
String s=in.nextLine();//读入空行到s
```



#### 12. kmp算法

> O(n)的字符串匹配算法——多个匹配都要输出

1. 原字符串，查找s中和p匹配的字串
2. 原字符串 $s$ 的下标 $i$ 与匹配字符串 $p$ 的下标 $j+1$ 相对应，所以 $i$ 初始化为1， $j$ 初始化为0（在预处理 $next$ 数组时， $ne[1]=0$ ，所以 $i$ 从2开始）

3.  $i$ 从哪里开始？
    1. 预处理 $next$ 数组时， $i$ 从2开始，如果从1开始，串 $p$ 和自身全匹配， $next$ 数组直接从1到n了；

    2. 进行kmp匹配时，每一位都要尝试匹配，所以 $i$ 从1开始

4. $next$ 数组的处理方式和匹配过程类似

5. 匹配成功后，回退到前后缀相同的位置，即 $ne[j]$ ，开始下一次匹配

<img src="http://raw.githubusercontent.com/cys02/ImageRepository/master/IMG_66597E449012-1.jpeg" alt="IMG_66597E449012-1" style="zoom:50%;" />

**C++**

```
int n,m;
string p,s;
int ne[100005];

void solve(){
    cin>>n>>p>>m>>s;
    p=" "+p;
    s=" "+s;
    
    //预处理next数组——和自身进行kmp匹配
    ne[1]=0;
    for(int i=2,j=0;i<=n;i++){
    	//如果j==0，直接从1(即j+1)开始匹配就可以了
        while(j>0&&p[i]!=p[j+1])j=ne[j];
        if(p[i]==p[j+1])j++;
        ne[i]=j;
    }
    
    //kmp匹配
    for(int i=1,j=0;i<=m;i++){
        while(j>0&&s[i]!=p[j+1])j=ne[j];
        if(s[i]==p[j+1])j++;
        if(j==n){
            cout<<i-n+1-1<<" ";//原题下标从0开始，减去1
            j=ne[j];//回退到前后缀相同的位置，开始下一次匹配
        }
    }
    cout<<endl;
    
}
```







#### 14. Tire树（字典树）

> 高效的存储和查找字符串集合的数据结构

特点：字符的个数不是很多——全是数字 $or$ 全是小写字母 $or$ 全是大写字母

1. son数组的每一行充当了链表中的每个节点，==son中的每个元素实际指向了son的另一个行号，也就是另一个节点==
2. 该方法的关键在于idx，idx始终自然增长
3. 从上往下遍历时，如果该节点已经存在过，直接走过去，如果在当前这这条路上没有这个字母，则新创建一个节点用于岔开一条新的路
4. 在查询的过程中，逻辑上沿字典树的分叉一路向下，实则在son数组中跳跃（从son的前些行向后些行跳跃）
5. 插入字符串的过程中，`p=son[p][v];`表示p前往新的节点，可能是上一个节点的已有延续，也可能是新建出来的节点

##### 14.1 常见问题：[最大异或对](https://www.acwing.com/problem/content/145/)

利用字典树以接近O(n)的时间复杂度求出数组中的最大异或对

##### 14.2 常见问题：[最大异或和](https://www.acwing.com/problem/content/3488/)

提供了从树中删除一个元素的思想——定义数组 $cnt$ 记录每个节点的子树包含多少个数，在插入/删除的过程中给 $cnt[p]$ 加1或者减1



**C++**

```
#include<bits/stdc++.h>
//#define int long long
#define endl '\n'
using namespace std;
typedef long long LL;
typedef unsigned long long ULL;

//son数组的每一行充当了链表中的每个节点，son中的每个元素实际指向了son的另一个行号，也就是另一个节点
//题目说明所有字符串总长度不超过1e5，所以总结点数小于1e5，son可设置为1e5+5行
int son[100005][26];//1e5为所有字符串最大长度的和，在每一个节点处可能有26种不同的选择——对应26个小写字母
int cnt[100005];//存储以当前这个点结尾的单词有多少个
int idx;//表示当前用到哪个下标，下标是0的点，是根结点，也是空节点

//存入字符串操作
void insert(string str){
    int p=0;//p表示当前的根结点，从0开始
    for(int i=0;i<str.size();i++){
        //遍历需要寻入的字符串中的每一个元素
        int v=str[i]-'a';
        if(son[p][v]==0){
            //该方法的关键在于idx，idx始终自然增长
            //从上往下遍历时，如果该节点已经存在过，直接走过去，
            //如果在当前这这条路上没有这个字母，则新创建一个节点用于岔开一条新的路
            //idx的自然增长保证了每条路上得以匹配的节点一路向下，新增加的节点是独立的
            //其实也就是，数组son的每一行充当了链表中的一个节点
            //在查询的过程中，逻辑上沿字典树的分叉一路向下，实则在son数组中跳跃
            son[p][v]=++idx;
        }
        //p前往新的节点，可能是上一个节点的已有延续，也可能是新建出来的节点
        p=son[p][v];//p一定要从根节点走到新来的元素，如果有边，直接走过来；否则新建一条边走过来
    }
    //插入完成后p到达字符串的最后一个节点，则以该字符结尾的元素多了一个，处理cnt数组
    cnt[p]++;
}

//查询字符串的操作——查询某字符串已经出现过多少次，和插入操作很相似
int query(string str){
    int p=0;//根结点
    for(int i=0;i<str.size();i++){
        //遍历该字符串中的每一个元素
        int v=str[i]-'a';
        if(son[p][v]==0)return 0;//如果有元素对应不上，直接返回0
        p=son[p][v];
    }
    return cnt[p];
}
```

**Java**

```
static int[][] son = new int[100005][26];
static int[] cnt = new int[100005];
static int idx=0;

static void insert(char[] str) {
	int p=0;//当前根结点
	for(int i=0;i<str.length;i++) {
		int v=str[i]-'a';
		if(son[p][v]==0)son[p][v]=++idx;//注意是++idx，第一个节点赋值为1
		p=son[p][v];
	}
	cnt[p]++;
}

static int query(char[] str) {
	int p=0;
	for(int i=0;i<str.length;i++) {
		int v=str[i]-'a';
		if(son[p][v]==0) {
			return 0;
		}
		p=son[p][v];//注意p延伸到下一个节点
	}
	return cnt[p];
}
```



#### 15. 异或性质

> 异或：二进制下的不进位加法
>

1.  $a\oplus b=s$ 则 $a=s\oplus b$ 

2. 异或前缀和： $s=a_1\oplus a_2\oplus a_3\oplus... \oplus a_n$ ，满足前缀和的性质： $XOR(i,j)=s[j]\oplus s[i-1]$，  $XOR(i,j)$ 表示 $i$ 到 $j$ 的异或前缀和





#### 16. 广度优先搜索（BFS）

> 优势：可以搜到最短路

所有距离为1的点都搜到->距离为2->...->距离为n（每个点权重一样）

注意：==加入队列的时候就要改变 $vis$ 数组的值，防止二次加入队列==，不然在搜索的过程中，一个位置在==出队之前==被多个临近的元素访问到时，就会重复加入

**C++模版：**[AcWing 844. 走迷宫](https://www.acwing.com/problem/content/submission/code_detail/22626151/)

**C++**

```
int dx[]={0,0,0,1,-1};
int dy[]={0,1,-1,0,0};

int a[110][110];
int vis[110][110];

void solve(){
    int n,m;
    cin>>n>>m;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            cin>>a[i][j];
        }
    }
    
    queue<pair<int,int>> que;
    que.push({1,1});
    vis[1][1]=1;
    while(!que.empty()){
        pair<int, int> p=que.front();
        que.pop();
        for(int i=1;i<=4;i++){
            int x=p.first+dx[i];
            int y=p.second+dy[i];
            if(x>=1&&x<=n&&y>=1&&y<=m&&vis[x][y]==0&&a[x][y]==0){
                que.push({x,y});
                vis[x][y]=vis[p.first][p.second]+1;
            }
        }
    }
    cout<<vis[n][m]-1<<endl;
}
```



#### 17. 深度优先搜索（DFS）

> 没有模板QAQ

典型问题：[八皇后](https://www.acwing.com/problem/content/845/)

##### 对角线和反对角线的处理方式

<img src="http://raw.githubusercontent.com/cys02/ImageRepository/master/IMG_0455.jpg" alt="IMG_0455" style="zoom: 67%;" />

**解题方式：**

1. 使用三个数组： $col[n]$ 、 $dg[2n]$ 、 $udg[2n]$ 分别对列、对角线和反对角线做唯一标识，使用dfs求的结果
2. 由于 $i+j$ 和 $n-i+j$ 最大为 $2n$ ，注意数组大小
3. 反对角线的处理过程中， $n-i+j$ 和 $n-j+i$ 效果相同

**C++**

```
int n;
int col[10];//表示某一列是否被占用
int dg[20];//表示某一个对角线是否被占用，(i+j)最大为2n
int udg[20];//表示某一个反对角线是否被占用，(j-i)可能为负数，加一个n，最大为2n
int mp[10][10];

void dfs(int i){
    if(i>n){
        for(int ii=1;ii<=n;ii++){
            for(int j=1;j<=n;j++){
                if(mp[ii][j]==1)cout<<'Q';
                else cout<<'.';
            }
            cout<<endl;
        }
        cout<<endl;
    }
    
    for(int j=1;j<=n;j++){
        //查看所有可能的选择
        if(col[j]==0&&dg[i+j]==0&&udg[n-i+j]==0){
            col[j]=dg[i+j]=udg[n-i+j]=1;
            mp[i][j]=1;
            dfs(i+1);
            mp[i][j]=0;
            col[j]=dg[i+j]=udg[n-i+j]=0;
        }
    }
    
}

void solve(){
    cin>>n;
    dfs(1);
}
```



#### 18. 拓扑排序

[拓扑排序模版](https://www.acwing.com/problem/content/850/)



### 图论

#### 19. 最短路算法

1. 有向图和无向图没有区别，无向图建图时双向加边
2. 稠密图用邻接矩阵，稀疏图用邻接表

<img src="https://raw.githubusercontent.com/cys02/ImageRepository/master/IMG_0456.jpg" alt="IMG_0456" style="zoom: 33%;" />

#### 20. Dijkstra求最短路

~~直接学堆优化版本的吧~~

使用**优先队列**，每个点可能有冗余的加入，复杂度为**O(mlogm)**

需要注意的问题：

1. **关于 $vis[]$ 数组：**
    1. 在某些BFS中，加入队列时更新 $vis[]$ 数组，保证每个节点只入队一次
    2. 在Dijstra中，只有确定一个节点的离原点的值后（也就是确定 $dist[]$ 后），才能不再继续考虑这一个点。
    3. 也就是只有点 $i$ **出队并完成处理后**，到这个节点的最短路才算确定， $dist[i]$ 不会再更新，**后续也不会有更短的路**，此时 $vis[i]$ 才可以设置为1。

**C++模版**

[Dijkstra模版](https://www.acwing.com/problem/content/submission/code_detail/22688186/)

**Java模版**

[Dijkstra模版](https://www.acwing.com/problem/content/submission/code_detail/22690431/)





#### 21. SPFA求最短路

> 可以处理负边权

##### 21.1 Bellman-Ford算法

> 时间复杂度O(nm)

1. 两层循环，第一层遍历n次，第二层循环所有边，使得 $dist[b]=min(dist[b],dist[a]+w)$ 
2. 操作后， $dist[b]\leq dist[a]+w$ 
3. 上面的不等式叫做：**三角不等式**；操作过程叫做：**松弛操作**



**21.2 SPFA算法**

> 对Bellman-Ford算法的优化——只有a更新了，b才可能会更新，时间复杂度O(m)~O(nm)

基本思路：==更新过谁，就加入队列，然后再拿它去更新别人==

~~和不加堆优化的dijkstra很像~~

**C++模版**

[SPFA模版](https://www.acwing.com/problem/content/submission/code_detail/22756634/)



**21.3 使用SPFA判断负环**

1. 定义一个 $cnt$ 数组，每当一个点的最短距离被更新的时候就使 $cnt[v]=cnt[u]+1$ ，当 $cnt[v]>n$ 的时候，就说明有负环存在了
2. 一开始所有的节点都要存入队列， 因为可能有第一个节点到不了的点
3. 只关心有无负环而不关心最短距离，可以不初始化 $dist$ 数组的大小



#### 22. Floyd求最短路

注意：

1. ==用邻接矩阵的话，同一个点的初始值要设置为0==

    `if(i==j)mp[i][j]=0;else mp[i][j]=INF;`

2. ==初始化时，新输入的边长和原来的边长取最小值==

​	`mp[i][j]=min(mp[i][j],mp[i][k]+mp[k][j]);`

3. 如果有负权边的话，==最后的结果可能比 $INF$ 小一点==，所以如果最后的值比 $INF/2$ 大，就是没有到这这个点的边

​	`if(mp[u][v]>INF/2)cout<<"impossible"<<endl;`

**C++模版**

[Floyd模版](https://www.acwing.com/problem/content/submission/code_detail/22769118/)





#### 23. Prim算法求最小生成树

朴素Prim算法：

**C++模版：**[Prim算法](https://www.acwing.com/problem/content/submission/code_detail/22777990/)

> 堆优化的Prim算法不好写，不如写Kruskal，没有学的必要





#### 22. Kruskal算法求最小生成树

1. 将所有边按权重从小到大排序
2. 从小到大枚举每条边 $ab$ ，如果 $a$ 和 $b$ 不连通，就把这条边加入边的集合

3. 构造生成树的过程中记录边数，如果边数小于（点的个数-1），则没有生成树

**C++模版**

[Kruskal算法](https://www.acwing.com/problem/content/submission/code_detail/22778572/)



##### 22.1 C++中的两种重载小于号

> 用于结构体排序

```
typedef struct{
    int u,v,w;
} Edge;

bool operator<(Edge a,Edge b){//重载小于号，按边权排序
    return a.w<b.w;
}
```

```
//注意这一种的写法
struct Edge{
    int u,v,w;
    bool operator < (const Edge &e)const{
        return w<e.w;
    }
}edge[N];
```





#### 最近公共祖先LCA

##### 倍增做法

> 在线做法
>
> 预处理 $O(nlogn)$ ，查询 $O(logn)$ 

1.  $fa[i,j]$ 表示==从 $i$ 开始，向上走 $2^j$ 步能走到的节点==， $0\leq j\leq logn$ 
    1. 构造方法： $fa[j][k]=fa[fa[j][k-1]][k-1]$ ，含义是， $j$ 走 $2^{k-1}$ 步能到的点，再走 $2^{k-1}$ 步，就相当于 $j$ 走了 $2^k$ 步
2.  $depth[i]$ 表示节点 $i$ 的深度
    1. 构造方法： $BFS$ 
3. 步骤：
    1. 先将两个点跳到同一层
    2. 让两个点同时往上跳，一直跳到他们最近公共祖先的下一层
    3. 为了简化操作，设置“哨兵”：
        1. 原因：向上跳的过程中可能跳到根结点之上
        2. 做法：规定 $depth[0]=0$ 

**LCA倍增模版（C++）**[AcWing 1172. 祖孙询问](https://www.acwing.com/problem/content/submission/code_detail/22875141/)

**典型应用：求二叉树中任意两点的距离（C++）**[AcWing 3555. 二叉树](https://www.acwing.com/problem/content/submission/code_detail/22879892/)

模版：[P3379 【模板】最近公共祖先（LCA）](https://www.luogu.com.cn/problem/P3379)

重要函数注释解释：

```
void bfs(){
    depth[0]=0;//设置下标为0的点（即根结点之上的点）为“哨兵”，
    		   //这样后续跳到根结点之上的时候，返回的是0
    depth[root]=1;//根结点的层数为1
    queue<int> que;//bfs初始化depth数组和fa数组
    que.push(root);
    while(!que.empty()){
        int u=que.front();
        que.pop();
        for(int i=head[u];i!=-1;i=edge[i].next){
            int v=edge[i].to;
            if(depth[v]==INF){
                depth[v]=depth[u]+1;//初始化depth数组
                que.push(v);
                fa[v][0]=u;//2的0次方为1，从v点向上走1步可以到u;
                for(int k=1;k<=16;k++){//2的16次方可以大于题目要求的40000
                    fa[v][k]=fa[fa[v][k-1]][k-1];//重要的递推
                }
            }
        }
    }
}

int lca(int x,int y){
    if(depth[x]<depth[y])swap(x,y);//首先让x比y更深
    //第一步，让x到达和y相同的深度
    
    //这里体现出了“哨兵”的作用：因为根结点在fa数组中的值都为0，
    //所以走的步数过多以至于超出根结点时，会来到0节点，而0节点的深度为0，
    for(int k=16;k>=0;k--){
        if(depth[fa[x][k]]>=depth[y]){
            //这里的意思是，一定要保证在x节点上升后，其深度仍然深于y或与y相同，
            //才能往上升，最后的结果是x上升到和y相同的深度
            
            //这里体现出了“哨兵”的第一个作用：因为根结点在fa数组中的值都为0，
            //所以走的步数过多以至于超出根结点时，会来到0节点，而0节点的深度为0，
            //不满足depth[fa[x][k]]>=depth[y])，就不会执行if中的语句
            x=fa[x][k];
        }
    }
    
    //第二步，找最近公共祖先
    
    //经过x节点的上移，x和y节点来到了同一个位置,说明x和y的最近公共祖先是x或者y
    if(x==y)return x;
    
    //否则使其一起上移，直到再往上移动一个节点，两个节点就相同了
    for(int k=16;k>=0;k--){
        //这里体现了“哨兵”的第二个作用：当前x和y在同一层，如果跳出去了，则都跳出去，
        //都跳出去则深度都为0，不满足fa[x][k]!=fa[y][k]，符合条件
        //注意这里就不是深度了，要判断x和y一起上升后是不是同一个元素
        if(fa[x][k]!=fa[y][k]){
            x=fa[x][k];
            y=fa[y][k];
        }
    }
    //再往上移一个节点，就是x和y的最近公共祖先
    return fa[x][0];
}
```



##### Tarjan做法

> 离线做法
>
> 时间复杂度： $O(n+m)$ ，节点数量n，查询数量m

在深度优先遍历时，将所有的点分为三类：

1. 已经遍历过，且回溯过的点
2. 正在搜索的分支
3. 还未搜索到的点

~~还没有细学，代码也不会写~~



##### 优化最小生成树算法

> 将 $O(n^2)$ 的最小生成树算法优化成 $O(nlogn)$ 

~~还没学~~



#### 二分图

> 二分图：每两个相邻的节点颜色不同，图中可能存在重边和子环
>
> 一个图是二分图，当且仅当图中不含奇数环

##### 染色法判定二分图

采用 $dfs$ 

**C++模版：**[AcWing 860. 染色法判定二分图](https://www.acwing.com/problem/content/submission/code_detail/22989330/)



##### 匈牙利算法

> 给定一个二分图，求它的最大匹配
>
> 时间复杂度 $O(nm)$ ，实际运行时间一般远小于 $O(nm)$ 

二分图匹配？

给定两类点，每一个第一类点有多个第二类点可以进行匹配，问最大的匹配对数

思路：

1. 对于每个第一类点 $i$ ，遍历它的所有选择
2. 如果 $i$ 的选择已经被其他的第一类点 $j$ 选择了，尝试让 $j$ 匹配别的第二类点，而让出 $i$ 想要匹配的这一个
3. 注意每次对第一类点进行判断时都要清空 $vis$ 数组



**C++模板：**[AcWing 861. 二分图的最大匹配](https://www.acwing.com/problem/content/submission/code_detail/22995487/)（注意注释内容）



**注意注释内容：**

```
const int N=510,M=100005;

struct Edge{
    int to,next;
}edge[M];
int head[N];
int idx=1;

int match[N];//match数组第二类点目前正在和那个第一类点相匹配，为0表示还没有匹配
bool vis[N];//在搜索过程中判重，表示对于一个第一类点，以及在让别的第一类点改变匹配的过程中，
            //每个第二类点只考虑一次

void add(int u,int v){
    edge[idx].to=v;
    edge[idx].next=head[u];
    head[u]=idx++;
}

bool find(int u){
    for(int i=head[u];i!=-1;i=edge[i].next){//遍历该节点所有能匹配的第二类节点
        int v=edge[i].to;
        if(!vis[v]){//该第二类节点还没有访问过
            vis[v]=true;//该第二类节点已经访问过，不要多次访问
            if(match[v]==0||find(match[v])){//该第二类节点还没有匹配，或者虽然已经匹配
                                            //但能为匹配的第一类节点找到另一个匹配的二类节点
                match[v]=u;
                return true;
            }
        }
    }
    return false;
}


void solve(){
    int n1,n2,m;
    cin>>n1>>n2>>m;
    memset(head, -1, sizeof(head));
    for(int i=1;i<=m;i++){
        int u,v;
        cin>>u>>v;
        add(u,v);
    }
    int ans=0;
    for(int i=1;i<=n1;i++){
        memset(vis, 0, sizeof(vis));//对于每一个第一类节点的匹配过程，所有的第二类节点都有可能被
                                    //重新尝试，所以要清空
        if(find(i))ans++;   //匹配成功
    }
    cout<<ans<<endl;

}
```









#### 树状数组

> 1. 在某个位置加上一个数O(logn)——单点修改
>
> 2. 快速求前缀和O(logn)——区间查询
> 3. 配合差分的思想，可以实现区间修改&单点查询或者区间修改&区间查询
> 4. 把某个数变成另一个数？(x+(-x)+v)

##### lowbit函数

`lowbit(int x){return x&(-x)};`

返回二进制下==最低位的1所代表的十进制数==

树状数组 $c$ 中的每一个元素 $c[x]=sum(c[x]-lowbit(x),x]$ ，为左开右闭的区间和

**C++模版：**[树状数组模版](https://www.acwing.com/problem/content/submission/code_detail/22784559/)

**经典题目：**[洛谷P1908 逆序对](https://www.luogu.com.cn/problem/P1908)（树状数组、OR归并排序）~~归并排序不会~~

关键函数

```
int lowbit(int x){
    return x&(-x);
}

void add(int x,int cnt){
    for(int i=x;i<=n;i+=lowbit(i)){
        tr[i]+=cnt;
    }
}

int query(int x){
    int ret=0;
    for(int i=x;i>0;i-=lowbit(i)){
        ret+=tr[i];
    }
    return ret;
}
```





#### 线段树

线段树的操作：

 $pushup$ ：更新父节点

 $pushdown$ ：将父节点的变化下放到子节点（涉及懒标记）

 $build$ ：建树

 $motify$ ：修改——单点修改、区间修改（结合 $pushdown$ ，懒标记 ）

 $query$ ：查询操作——查询区间的最大值和最小值



易错点总结：

1. 建树的时候不要忘了给 $l$ 和 $r$ 赋值`tr[u].l=l;tr[u].r=r;`
2. $build$ 和 $motify$ 一般都要在结尾进行`pushup(u);`

3. 记得 $bulid(1,1,n)$ 建树



**不需要懒标记：**

**例题1：**区间最值（没有 $modify$ ）

（C++）[AcWing 1270. 数列区间最大值](https://www.acwing.com/problem/content/submission/code_detail/22887429/)

（Java）[AcWing 1270. 数列区间最大值](https://www.acwing.com/problem/content/submission/code_detail/22892963/)

**例题2：**单点修改，区间最值

（C++）[AcWing 1275. 最大数](https://www.acwing.com/problem/content/submission/code_detail/22843604/)（这个题的build函数和模版有区别， $pushup$ 函数可以不写）

（Java）[AcWing 1275. 最大数](https://www.acwing.com/problem/content/submission/code_detail/22843604/)

**例题3：**单点修改，区间求和

（C++）[AcWing 245. 你能回答这些问题吗](https://www.acwing.com/problem/content/submission/code_detail/22848388/)

**例题4：**利用差分（配合gcd的性质），用单点修改模拟区间修改

（C++）[AcWing 246. 区间最大公约数](https://www.acwing.com/problem/content/submission/code_detail/22861154/)



##### 懒标记

懒标记：给以当前节点为根的子树中的每一个节点，都加上一个 $x$ ，但**不包含这个根结点**

-  一定要保证懒标记适用于**当前这个完整的区间**，凡是需要裂开的时候就往下传


- 在区间加的题目中
    - `sum`表示，如果**考虑当前节点及子节点上的所有标记**，当前区间的和是多少
    - `add`表示，给**当前区间的所有儿子**加上`add`

- `pushup`函数放在`build`函数和`modify`函数的最后
- `pushdown`函数放在`modify`函数和`query`函数`else`中的最前

**例题1：**区间修改，区间求和

（C++）[AcWing 243. 一个简单的整数问题2](https://www.acwing.com/problem/content/submission/code_detail/23051274/)

（Java）[AcWing 243. 一个简单的整数问题2]()



### 数论

#### 数学结论

#####   $x$ 和 $y$ 最大的不能表示出的数

1. 给定  $x$ 和 $y$ ，若 $gcd(x,y)>1$ ，则一定不能凑出最大数
2. 如果  $x$ 和 $y$ 均是正整数且互质，那么由 $ax+by,a\geq0,b\geq0$ ，不能凑出的最大的数是 $xy-x-y$



#####  gcd的结合律

 $gcd(a_1,a_2,a_3,...,a_n)=gcd(a_1,a_2-a_1,a_3-a_2,...,a_n-a_{n-1})$ 



#####  中位数的性质

给定n个点，使他们到某个点的距离之和最小，这个点是他们的中位数`(n+1)>>1`



#####  1到n之间的质数

1到n之间的质数有 $ln(n)$ 个



#### 最大公约数gcd

模板：

```
int gcd(int a,int b){
    return (b==0)?a:gcd(b,a%b);
}
```



#### 因数分解定理

> 算数基本定理

**所有正整数**都可以分解**为若干个质因子乘积**的形式



#### 约数公式

##### 约数个数公式

> 求某个数的约数个数。比如，12的约数有：1，2，3，4，6，12，共6个

根据因数分解定理，

 $N=p_1^{\alpha _1}+p_2^{\alpha _2}+...+p_k^{\alpha _k}$ 

 $N$ 的约数个数为 $(\alpha_1+1)*(\alpha_2+1)*...*(\alpha_k+1)$ 

比如： $12=2^2*3^1$ ，约数个数为 $(2+1)*(1+1)=6$ 



##### 约数和公式

约数和= $(1+p_1+p_1^2+...+p_1^{\alpha1})*(1+p_2+p_2^2+...+p_2^{\alpha2})*...*(1+p_k+p_k^2+...+p_k^{\alpha k})$ 

比如： $12=2^2*3^1$ ，约数和为 $(1+2+2^2)*(1+3)=28=(1+2+3+4+6+12)$ 



#### 裴蜀定理

1. 对于任意一对正整数 $a$ 和 $b$ ，一定存在 $x$ 和 $y$ ，使得 $ax+by=gcd(a,b)$ 
2. 而且 $gcd(a,b)$ 是 $a$ 和 $b$ **能凑出的最小的正整数**



#### 扩展欧几里得算法

> 给定 $a$ 和 $b$ 求裴蜀定理中的 $x$ 和 $y$ 

1. 对于 $gcd(a,b)=d$ ，必然存在整数（可能是负数） $x$ 和 $y$ ，使得 $ax+by=d$ 
2. 对于 $gcd(a,b)=d$ ， $ax+by=d'$ ，则 $d'$ 是 $d$ 的倍数

以上两点对应**裴蜀定理**

欧几里得算法： $gcd(a,b)=gcd(b,a\%b)$ 

公式推导：

<img src="http://raw.githubusercontent.com/cys02/ImageRepository/master/9c31d4bb64ca1136d61231c6d9fdfd0a.jpg" alt="9c31d4bb64ca1136d61231c6d9fdfd0a" style="zoom:50%;" />

Java模版：[AcWing 877. 扩展欧几里得算法 ](https://www.acwing.com/problem/content/submission/code_detail/23954755/)

例题：[AcWing 878. 线性同余方程](https://www.acwing.com/problem/content/880/)

求逆元：[AcWing 876. 快速幂求逆元-exgcd做法](https://www.acwing.com/problem/content/submission/code_detail/23958793/)

扩欧求逆元模版：[https://www.luogu.com.cn/problem/P1082](https://www.luogu.com.cn/problem/P1082)

关键代码：

```
static class Ret{
	int d,x,y;
	Ret(int d,int x,int y){
		this.d=d;
		this.x=x;
		this.y=y;
	}
}

static Ret exgcd(int a,int b) {
	if(b==0) {
		return new Ret(a,1,0);//当b为0时，最大公约数取1，此时x取1，y取0，满足ax+by=0;
	}
	Ret act=exgcd(b,a%b);
	int x=act.x,y=act.y;//经过公式推导，返回的x为y，返回的y为(x-a/b*y);
	return new Ret(act.d,y,x-a/b*y);
}
```



#### 中国剩余定理

用于求解下面的方程组：

<img src="/Users/lovelylittlemon/Library/Application Support/typora-user-images/image-20230330204610065.png" alt="image-20230330204610065" style="zoom:50%;" />



注意是==对 $n_i$ 取余，余数是 $a_i$== ，==其中 $n_1,n_2,...,n_k$ 互质==

解决步骤：

1. 计算 $b_1,b_2,...,b_k$ 的积 $B$ 
2. 对于每个方程，求出 $m_i=\cfrac{B}{b_i}$ ，由于数组 $b$ 中两两互质， $m_i$ 和 $m_i$ 互质
3. 对于每个方程，计算出 $m_i$ 在模 $b_i$ 意义下的逆元 $m_i^{-1}$ 
4. 对于每个方程，计算出 $c_i=b_i*b_i^{-1}$ 
5. 方程在模 $N$ 意义下的解： $x=\sum_{i=1}^{k}a_i*c_i(mod\ N)$ **记得要对B取余**

例题：[P1495 【模板】中国剩余定理（CRT）/ 曹冲养猪](https://www.luogu.com.cn/problem/P1495)



#### 质数筛

##### 埃氏筛

> 时间复杂度 $O(nloglogn)$ ，接近于 $O(n)$ 

思想：

1. 每找到一个质数，令其倍数都判为合数
2. 过程中没有被标记的数就是质数

例题：[AcWing 868. 筛质数](https://www.acwing.com/problem/content/submission/code_detail/22944271/)

关键函数：

```
const int N=1000005;
int prime[N],cnt=0;//记录质数和质数的个数
bool isprime[N];//true表示合数

void getprime(int n){
    for(int i=2;i<=n;i++){
        if(isprime[i])continue;//已经确定是合数了就continue
        prime[++cnt]=i;
        for(int j=i+i;j<=n;j+=i){//如果是质数的话，它的倍数都是合数
            isprime[j]=true;
        }
    }
}
```



##### 线性筛法

> 时间复杂度 $O(n)$ ，比埃氏筛快一倍

核心思想： $n$ 只会被它的最小质因子筛掉

1.  $prime[j]$ 从小到大遍历，当`i%prime[j]==0`时， $prime[j]$ 一定是 $i$ 的最小质因子，也一定是 $prime[j]*i$ 的最小质因子。此时break是因为：如果 $prime[j]$ 可以被 $i$  整除且没有`break`，那么对于 $prime[j+1]$ （比 $prime[j]$ 大的质数）和 $i$ 相乘的最小质因子是 $prime[j]$ 而不是 $prime[j+1]$ ，在此之前已经被筛过了，不需要重复筛
2. 当`i%prime[j]!=0`时， $prime[j]$ 小于 $i$ 的最小质因子，小于 $prime[j]*i$ 的最小质因子
3. 又因为每个数只有一个最小质因子，所以每个数只会被它的最小质因子筛掉
4. 关于遍历所有已知质数时`j<=cnt`（循环中的限制）可写可不写
    1. 当 $i$ 是合数， $prime[j]$ 为 $i$ 的最小质因子时会break， $j$ 不会越界
    2. 当 $i$ 是质数，按照程序执行的顺序，此时 $prime[]$ 数组中的最后一个元素是 $i$ ，当 $prime[j]=i$ 时， $i\%prime[j]=0$ ，满足程序`break`的的条件

例题：[AcWing 868. 筛质数](https://www.acwing.com/problem/content/submission/code_detail/22948137/)

关键函数：

```
const int N=1000005;
int prime[N],cnt=0;//记录质数和质数的个数
bool isprime[N];//true表示合数，false表示质数

void getprime(int n){
    for(int i=2;i<=n;i++){
        if(!isprime[i])prime[++cnt]=i;
        for(int j=1;j<=cnt&&prime[j]*i<=n;j++){//遍历已知的所有质数，j<=cnt可以不写
            isprime[prime[j]*i]=true;
            if(i%prime[j]==0)break;
        }
    }
}
```







#### 快速幂

> 以 $O(logk)$ 的时间复杂度求出 $a$ 的 $k$ 次方（对 $p$ 取余）的结果
>

主要思想：

1. 将 $k$ 拆成 $2^0,2^1...2^{logk}$ ，也就是将 $a^k$ 拆分成 $a^{2^0},a^{2^1}...a^{2logk}$ 之和，采用二进制思想
2. 每次判断 $k$ 的最低位，如果是1， $ret$ 要加上当前的 $a$ ，表示 $k$ 拆分后该二进制位取1
3. 使 $k$ 右移一位，此时基数代表的值应该翻倍，使 $a=a*a$ 
4. 注意计算过程中 $a$ 要转换为`long long`

**C++模版：**[AcWing 875. 快速幂](https://www.acwing.com/problem/content/submission/877/)

关键函数：（ $a$ 的 $b$ 次方对 $p$ 取余）

```
LL poww(int a,int b,int p){//a的b次方对p取余
    LL ret=1%p;
    while(b){
        if(b%2==1){
            ret=ret*(LL)a%p;//类型转换为longlong
        }
        b/=2;
        a=(LL)a*a%p;//类型转换为longlong
    }
    return ret;
}
```



#### 乘法逆元

##### 费马小定理+快速幂求乘法逆元

逆元？

 $\cfrac{a}{b} \equiv a\ast b^{-1} (\%m)$

==注意： $a$ 和 $m$ 互质才有逆元，否则没有逆元==

解释：同余 $m$ 下， $a$ 除以 $b$ 等于 $a$ 乘 $b$ 的逆元，用 $b^{-1}$ 表示

作用：将**除法**运算转化为**乘法**运算 

**费马小定理—— $p$ 是质数**

 $b^{p-1} \equiv 1 (\%p)$ 

所以， $b^{p-2} \equiv b^{-1} (\%p)$ 

利用快速幂计算 $b^{p-2}$ 

**无解情况？**

如果 $b$ 是 $p$ 的倍数，二者不互质，则无解

否则，由于 $p$ 是质数，二者一定互质，则一定有解

**C++模版：**[AcWing 876. 快速幂求逆元](https://www.acwing.com/problem/content/submission/code_detail/23014622/)



##### 扩展欧几里得求逆元

> 费马小定理要求取模的数一定是质数，扩欧不要求（但必须求逆元的输和取模的数互质）

[AcWing 876. 快速幂求逆元-exgcd做法](https://www.acwing.com/problem/content/submission/code_detail/23958793/)

[P3811 【模板】乘法逆元](https://www.luogu.com.cn/record/111027566)

[P1082 [NOIP2012 提高组] 同余方程](https://www.luogu.com.cn/problem/P1082)

##### 递推求逆元

`ans[i] = p-p/i*ans[p%i]%p;`

**模版题：**[洛谷P3811](https://www.luogu.com.cn/problem/P3811)



#### 求组合数

##### 递推公式

>  $a$ 和 $b$ 的范围适用于二维数组，适合打表预处理

 $C_a^b=C_{a-1}^b+C_{a-1}^{b-1}$ 

**C++模版：**[AcWing 885. 求组合数 I](https://www.acwing.com/problem/content/submission/code_detail/23068812/)

关键函数：

```
LL c[N][N];
void init(){
    for(int i=0;i<N;i++){
        for(int j=0;j<=i;j++){
            if(j==0)c[i][j]=1;
            else {
                c[i][j]=(c[i-1][j]+c[i-1][j-1])%MOD;
            }
        }
    }
}
```



##### 预处理阶乘和阶乘逆元

根据组合数公式： $C_a^b=\cfrac{a!}{b!(a-b)!}$ 

预处理阶乘数组`fact[]`和阶乘逆元的数组`infact[]`

**C++模版：**[AcWing 886. 求组合数 II](https://www.acwing.com/problem/content/submission/code_detail/23069366/)

关键代码：

```
LL fact[N];//记录阶乘
LL infact[N];//记录阶乘逆元
fact[0]=infact[0]=1;//初始化，0的阶乘为1

for(int i=1;i<N;i++){
    fact[i]=fact[i-1]*i%MOD;
    infact[i]=infact[i-1]*poww(i,MOD-2)%MOD;//费马小定理配合快速幂求i的逆元
}
```



#### 欧拉函数

> 表示小于等于n并和n互质的数的个数

求 $a$ 的：小于等于 $a$ 并和 $a$ 互质的数的个数 $ans$

```
int ans=a,x=a;
for(int i=2;i<=Math.sqrt(a);i++) {
	if(x%i==0) {
		ans=ans/i*(i-1);
		while(x%i==0)x/=i;
	}
}
if(x>1)ans=ans/x*(x-1);
out.println(ans);
```



#### 算术基本定理求约数个数

```
static int factors(long n) {
    int res = 1, now;
    for (int i = 2; i * i <= n; i++) {
        now = 1;
        while (n % i == 0) {
            n /= i;
            now++;
        }
        if (now > 1)
            res *= now;
    }
    return n > 1? res << 1 : res;
}
```







#### 博弈论

先手必胜状态：可以走到某一个必败状态

先手必败状态：走不到任何一个必败状态

例题：[AcWing 891. Nim游戏](https://www.acwing.com/problem/content/893/)

解题思路：

1. 异或和为0，拿走一部分后的异或和一定不是0；
2. 异或和不是0，肯定有一种方式拿走一部分使异或和为0
3. 所以，初始情况异或和为0，则先手拿到的异或和一直都是0，否则先手拿到的异或和一直都不是0；
4. 拿到和为0的人输掉比赛



SG函数的应用？~~还没学会呜呜呜~~

> 结合视频讲解食用

[AcWing 893. 集合-Nim游戏](https://www.acwing.com/problem/content/895/)



> Mex运算
> 设S表示一个非负整数集合。定义mex(S)为求出不属于集合S的最小非负整数的运算，即：
> mex(S) = min{x}, x属于自然数，且x不属于S

> SG函数
> 在有向图游戏中，对于每个节点x，设从x出发共有k条有向边，分别到达节点y1, y2, …, yk，定义SG(x)为x的后继节点y1, y2, …, yk 的SG函数值构成的集合再执行mex(S)运算的结果，即：
> SG(x) = mex({SG(y1), SG(y2), …, SG(yk)})
> 特别地，整个有向图游戏G的SG函数值被定义为有向图游戏起点s的SG函数值，即SG(G) = SG(s)。

> 有向图游戏的和 —— 模板题 AcWing 893. 集合-Nim游戏
> 设G1, G2, …, Gm 是m个有向图游戏。定义有向图游戏G，它的行动规则是任选某个有向图游戏Gi，并在Gi上行动一步。G被称为有向图游戏G1, G2, …, Gm的和。
> 有向图游戏的和的SG函数值等于它包含的各个子游戏SG函数值的异或和，即：
> SG(G) = SG(G1) ^ SG(G2) ^ … ^ SG(Gm)

> 定理
> 有向图游戏的某个局面必胜，当且仅当该局面对应节点的SG函数值大于0。
> 有向图游戏的某个局面必败，当且仅当该局面对应节点的SG函数值等于0。



### DP

#### 背包问题

##### 01背包

 $dp[i,j]=max(dp[i-1][j],dp[i-1][j-v[i]]+w[i])$ 

**C++模版：**[AcWing 2. 01背包问题](https://www.acwing.com/problem/content/submission/code_detail/23129203/)（二维）

转换到一维？

对于每一个元素，转移需要的内容来自于左侧和上侧，所以可以使用**滚动数组**优化掉一维

转移的内容来自于左侧：从上到下，**从右到左**遍历

**C++模版：**[AcWing 2. 01背包问题](https://www.acwing.com/problem/content/submission/code_detail/23129270/)（一维）



##### 完全背包

朴素做法：三重循环，遍历物品 $i$ 所有能放的个数

（帮助理解，大概率T）

```
for(int i=1;i<=n;i++){
    for(int j=0;j<=m;j++){
        for(int k=0;k*v[i]<=j;k++){
            dp[i][j]=max(dp[i][j],dp[i-1][j-v[i]*k]+w[i]*k);
        }
    }
}
```



**优化：**

可以注意到， $dp[i,j]$ 的最大值只和 $dp[i-1,j]$ 和 $dp[i,j-v[i]]+w[i]$ 有关

注意：

1. 完全背包中 $dp[i,j-v[i]]+w[i]$ 是**同一行**的
2. 01背包中 $dp[i-1][j-v[i]]+w[i]$ 是**上一行**的

![image-20230317093244446](http://raw.githubusercontent.com/cys02/ImageRepository/master/image-20230317093244446.png)

从而可以优化到**两重循环**和**一维**

**代码实现：**

由于处理当前 $dp[i,j]$ 时需要用到同一行的前面的元素，所以第二重循环**从前往后遍历**

```
for(int i=1;i<=n;i++){
    for(int j=0;j<=m;j++){
        if(j>=v[i]) dp[j]=max(dp[j],dp[j-v[i]]+w[i]);
    }
}
```

**C++模版：**[AcWing 3. 完全背包问题](https://www.acwing.com/problem/content/submission/code_detail/23132657/)





##### 多重背包

> 第 $i$ 件物品有 $s[i]$ 个

朴素版本的多重背包问题（和朴素版本的完全背包问题相同）

 $dp[i,j]=max(dp[i-1][j-v[i]*k]+w[i]*k);k=0,1,2,...,s[i]$ 



**朴素做法代码：** $O(n*m*s)$ 

```
for(int i=1;i<=n;i++){
    for(int j=0;j<=m;j++){
        for(int k=0;k<=s[i]&&k*v[i]<=j;k++){
            //k可以等于0，即dp[i][j]=dp[i-1][j]的情况，不用特殊判断
            dp[i][j]=max(dp[i][j],dp[i-1][j-k*v[i]]+k*w[i]);
        }
    }
}
```



优化方式：二进制倍增思想

**每一个数都能用2的次方数求和表示**

拼凑方法： $cnt=1+2+4+8+...+2^k+C$ 其中 $C$ 为一常数，一定小于 $2^{k+1}$ 

解题方法：

1. ==每件物品有 $S_i$ 个，将这 $S_i$ 个物品拆分成 $log_{2}S_i$ 件物品==，拆分后的每件物品相当于之前的 $1,2,4,8,...,2^k$ 件物品
2. 对拆分后的物品做01背包问题

**C++模版：**[AcWing 5. 多重背包问题 II](https://www.acwing.com/problem/content/submission/code_detail/23146543/)（注意注释内容）

长个记性：[AcWing 4877. 最大价值](https://www.acwing.com/problem/content/4880/)因为有重量的限制，可能一个也装不进去，应该从 $1$ 开始枚举 $k$ 

**关键代码：**

```
void solve(){
    int n,m;
    cin>>n>>m;
    
    for(int i=1;i<=n;i++){//将物品按2的次方数打包，并加入到“01背包”中
        int a,b,s;
        cin>>a>>b>>s;
        int k=1;//k表示将几个物品打包成一个
        while(k<=s){
            v[cnt]=a*k;
            w[cnt]=b*k;
            cnt++;
            s-=k;
            k*=2;
        }
        if(s>0){//如果还没有被减完，需要将剩余的物品打成一个包并加入“01背包”中
            v[cnt]=a*s;
            w[cnt]=b*s;
            cnt++;
        }
    }
    
    n=cnt-1;
    for(int i=1;i<=n;i++){//01背包
        for(int j=m;j>=0;j--){
            if(j-v[i]>=0)dp[j]=max(dp[j],dp[j-v[i]]+w[i]);//注意取最大值
        }
    }
    cout<<dp[m]<<endl;
    
}
```





##### 分组背包

> N组物品和容积是V的背包
>
> 每组物品有若干个，同一组内的物品最多只能选一个
>
> 求背包的最大价值

比01背包多一重循环——从一组物品中选出一个能使 $dp[j]$ 值最大的

**C++模版：**[AcWing 9. 分组背包问题](https://www.acwing.com/problem/content/submission/code_detail/23149038/)

**关键代码：**

```
for(int i=1;i<=n;i++){
    for(int j=m;j>=0;j--){//从第i组中选择一个物品，这个物品加入背包的方式和01背包类似
        for(int k=1;k<=s[i];k++){
            if(j>=v[i][k])dp[j]=max(dp[j],dp[j-v[i][k]]+w[i][k]);
        }
    }
}
```







#### 线性DP

> 递推方程有明显的线性关系

经典问题1：[AcWing 896. 最长上升子序列 II](https://www.acwing.com/problem/content/898/)

解决方法：

1.  $dp[i]=max(dp[1]+1,dp[2]+1,...,dp[i-1]+1)$ ，转移条件 $a[j]<a[i]$ ，时间复杂度 $O(n^{2})$ 
2. 二分找到目前队列中第一个比它大的值并进行替换，时间复杂度 $O(nlogn)$ 

其中，第一种方法可以在状态转移时记录从哪个状态转移而来，从而记录了最长的上升子序列是谁



经典问题2：[AcWing 897. 最长公共子序列](https://www.acwing.com/problem/content/899/)



#### 区间DP

> 定义的状态是一个区间

经典为题：[AcWing 282. 石子合并](https://www.acwing.com/problem/content/284/)

解题思路：

1.  $DP$ 数组记录合并到该区间所消耗的最小的代价和，也就是之前消耗的也要加上
2. 代价和等于左区间的代价和加上右区间的代价和，再加上该区间的所有元素的和
   1.  $f[l,r]=min(f[l,mid]+f[mid+1,r]+sum[l,r])$ 
3. 记录前缀和，从小区间到大区间遍历，做状态转移



#### 计数类DP

例题：[AcWing 900. 整数划分](https://www.acwing.com/problem/content/902/)



#### 数位DP

> 求1到n中'0'到‘9’每个数字出现的次数

1. 求一个区间 $[x,y]$ 中满足某种性质的数的个数，转换为 $f(Y)-f(X-1)$
2. 从“树”的形式考虑——分情况讨论





#### 状压DP

> 合理运用二进制

例题：[AcWing 291. 蒙德里安的梦想](https://www.acwing.com/problem/content/293/)（注意看注释）

解题思路：

1.  $dp[i.j]$ 代表的是第 $i$ 列，状态为 $j$ 时，对应的摆放方式的个数；另一种表述方式： $dp[i, j]$ 表示第 $i - 1$ 列伸出的状态是 $j$ 的方案数。
2. 考虑这一行有上一列延伸出来，为1；否则为0，以一串二进制的数字代表状态，则 $[0,2^{n-1}]$ 可以表示所有的状态 
3. 这一列的状态为 $j$ ，上一列的状态为 $k$ ，考虑在什么情况下状态可以转移：
   1. 横着放的木块中间的空行必须是偶数，否则无法放入竖着的木块，所以 $[j|k]$ （ $j$ 和 $k$ 取或）的值，不能有奇数个连续的0。（ $j$ 的某一行为1，则代表该列有木块从 $k$ 中延伸过来， $k$ 中的这一列有木块，k的某一行为1，代表 $k$ 的上一列延伸到 $k$ ，所以 $[j|k]$ 代表的是 $k$ 中的木块情况），可以设置一个`bool`类型的数组，预先处理所有 $[j|k]$ 的情况，并判断哪些可用；
   2.   $j$ & $k$ 必须为0，因为 $j$ 的某一行为1，代表有木块从 $k$  中延伸过来， $k$ 的这一行是这个木块开始的位置，一定不会有木块延伸自上一列。

例题：[矩阵计数](https://www.lanqiao.cn/problems/246/learning/?page=1&first_category_id=1&sort=students_count&name=%E7%9F%A9%E9%98%B5%E8%AE%A1%E6%95%B0)





#### 树形DP

例题：[AcWing 285. 没有上司的舞会](https://www.acwing.com/problem/content/287/)

 解题思路：

1.  $f[i,1]$ 表示取 $i$ 节点， $i$ 的子树的最大值；
2.  $f[i,0]$ 表示不取 $i$ 节点， $i$ 的子树的最大值；

3.  $j$ 为 $i$ 的子节点， $f[i,1]$ 可以从所有的 $f[j,0]$ 转换过来；
4.  $f[i,0]$ 可以从所有的 $f[j,0]$ 或所有的 $f[j,1]$ 转换而来



#### 记忆化搜索

例题：[AcWing 901. 滑雪](https://www.acwing.com/problem/content/903/)

解题思路：

1. 已经完成处理的 $dp[i,j]$ 直接返回，保证每一个点只处理一遍
2. 使用 $dfs$ 遍历所有比该节点高度低的点，保证最高的点能遍历到所有点



### 计算几何

#### 凸包

两向量的叉积是一个向量，垂直于这两个向量所在的平面。

叉积的**方向遵守右手螺旋定则**，所以当两向量叉积为负，新向量在旧向量的顺时针方向，旧向量直接出队

模板：[【模板】二维凸包](https://www.luogu.com.cn/problem/P2742)



**计算多边形面积？**

拆分为多个三角形，将三角形的两个边用向量表示，运用向量叉积求面积

![image-20230526200111757](http://raw.githubusercontent.com/cys02/ImageRepository/master/image-20230526200111757.png)

