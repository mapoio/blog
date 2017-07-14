---
layout:     page
title:      "最小生成树 - 最短路径"
subtitle:   "本专题讨论最小生成树与最短路径算法中各种算法的优化，差异，使用情况！"
description: 算法那么多，我该取哪种！
date:       2016-07-29 00:00:00
author:     "曾杰安"
author-img:  "/cdn.isheng.top/favicon.png"
header-img: "/cdn.isheng.top/img/tree-4.jpg"
special: true
sitemap:
  priority: "0.8"
  changefreq: "daily"
tags:
    - 数据结构与算法
---


> 这一节专题分享的是我个人对这几种算法的几种浅见，如有不对或者需要探讨的，直接评论吧！在最小生成树中，我们学习了两种常用的算法——Prim算法和Kruskal算法，而在最短路径中，我们学习了Dijkstra算法、SPFA算法、Floyd算法。这些算法在遇到不同的问题，不同的场景在性能上有所不同，而且这些算法还可以根据不同的场景进行优化，使得效率有所提升，这节专题讲的就是这几种算法的相似与差异之处，以及这几种算法常用的优化方法，如有不对，欢迎评论指正。  

> 这篇专题并不讲原始算法的实现与理解，但每种算法的优化介绍后面都有传送门，可以帮你理解这类算法，或者一些简单的acm题目用于练手的。

## Dijkstra算法与Prim算法  
>他们长得好像呀！

### 他们是一对的吧！  
为什么将Dijkstra算法与Prim算法一起讲呢，明明一个是求单源最短路径，一个是求最小生成树；因为他们真的长得很像，在他们的核心代码中只有几行代码的区别，而就是那几行代码使得结果完全不同，掌握其中一种，另一种也是很轻松就能明白的。    
他们核心代码的不同就是在对“边”进行路径松弛（我也没找到松弛的具体定义，暂且先用着）的时候，这恰好就是两个算法的核心，先上代码：  

```c++
/*Dijkstra算法(部分)*/
for(v=1;v<=n;v++)
        {
            if(e[u][v]<inf)
            {
              if(dis[v]>dis[u]+e[u][v]) /*对节点进行更新，计算源点到连通点的权*/
              {
                  dis[v]=dis[u]+e[u][v];
                  /*通常我们把上面这三行称之为松弛。*/
              }
            }
        }
```

```c++
//Prim算法(部分)
for(j=1;j<VNUM;j++)
            {
                if(addvnew[j]==-1&&edge[v][j]<lowcost[j])//更新当前点到连通点的权  
                {
                    lowcost[j]=edge[v][j];//此时v点加入Vnew 需要更新lowcost
                    adjecent[j]=v;      
                }
            }
```

上面就是两个算法的核心，Dijkstra算法利用“边”的松弛，也就是每次寻找的符合要求的点时，对它维护的最小权所在数组进行更新，计算源点到该点的其他连通点的权是否小于源点直接到该点的权。而Prim算法就省去计算源点到该点的连通点的权，而是直接计算该点到连通点的权在与初始值比较，两者算法都采用贪心的思想，寻求当前最优解，唯一不同的就是贪心策略不一样，导致最终结果不一样。  
值得一提的是两个算法前半部分几乎是一模一样，也就是这几行不太一样，这下只要记住一个算法，另一个差不多也该记住了，模板也只用存一个就好了 ^~^ !  
不过不要弄混了，Prim是计算最小生成树的，而Dijkstra算法是计算源点到其他点的最短路径，一个是全局，一个是局部。

### 两种算法的优化
>这年头不优化怎么混呀！

#### 它们的算法复杂度
在不使用二叉堆优先队列进行优化的情况下，Prim算法与Dijkstra算法的时间复杂度都为 **$$O(v^2)$$** (邻接矩阵)或者，看着这个公式有点头晕，这里v指的是 **顶点** ，e指的是 **边** 。所以很容易得知在稀疏图时算法效率比较低。

#### 优化方案
由于两个算法差不多，所以优化方案也是差不多的，这里主要优化的是 **稀疏图** 的情况下，也就是说边的数量大概小于$${n*(n-1)} / 4$$这么多的时候考虑使用堆优化，可以将算法复杂度降低到$$O(elog_{2}v)$$,优化方案：  
1. 将与源点相连的点加入堆，并调整堆。
2. 选出堆顶元素u（即代价最小的元素），从堆中删除，并对堆进行调整。  
3. 处理与u相邻的，未被访问过的，满足三角不等式的顶点   
  1): 若该点在堆里，更新距离，并调整该元素在堆中的位置。  
  2): 若该点不在堆里，加入堆，更新堆。  
4. 若取到的u为终点，结束算法；否则重复步骤2、3。  

全部代码如下：  

```c++
//堆优化的prim算法,基于STL的优先队列（也叫堆），没有手动写一个堆，方便点
#include <iostream>
#include <cstdio>
#include <cstring>
#include <queue>
#include <vector>
using namespace std;

const int N = 100005;
const int M = 1000005;
struct Edge{
    int u,v,w;
}edge[M];

struct node{
    int x,w;
    bool operator < (const node &a) const{
        return w > a.w;
    }
};

int n,m;
priority_queue<node> q;
vector<node> v[N];
int vis[N];

void build(){
    memset(vis,0,sizeof(vis));
    while(!q.empty())
        q.pop();
    int a,b,c;
    node tmp;
    for(int i = 0; i < m; i++){
        scanf("%d%d%d",&a,&b,&c);
        tmp.x = b;tmp.w = c;
        v[a].push_back(tmp);
        tmp.x = a;
        v[b].push_back(tmp);
    }
}

int prime(){
    int cost = 0;
    node cur;
    cur.x = 1;
    cur.w = 0;
    q.push(cur);
    int tt = 0;
    while(!q.empty() && tt < n){
        cur = q.top();
        q.pop();
        if(vis[cur.x])
            continue;
        vis[cur.x] = 1;
        cost += cur.w;
        tt++;
        int l = v[cur.x].size();
        for(int i = 0; i < l; i++){
            if(!vis[v[cur.x][i].x]){
                node tmp;
                tmp.x = v[cur.x][i].x;
                tmp.w = v[cur.x][i].w;
                q.push(tmp);
            }
        }
    }
    return cost;
}

int main(){
    while(~scanf("%d%d",&n,&m)){
        build();
        printf("%d\n",prime());
    }
    return 0;
}
```

```c++
//堆优化的Dijkstra算法,基于STL的优先队列(也叫堆)，没有手动写一个堆，方便点
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
const int N=405;
struct rec
{
    int v,w;
};
vector<rec> edge[N*N];
int n,st,ed;
__int64 dis[N*N];
bool vis[N*N];
struct cmp
{
    bool operator()(int a,int b)
    {
        return dis[a]>dis[b];
    }
};
void Dijkstra()
{
    priority_queue<int,vector<int>,cmp> Q;
    memset(dis,-1,sizeof(dis));
    memset(vis,0,sizeof(vis));
    int i,u,v;
    Q.push(st);
    dis[st]=0;
    while(!Q.empty())
    {
        u=Q.top();
        Q.pop();
        vis[u]=0;
        if(u==ed)
            break;
        for(i=0;i<edge[u].size();i++)
        {
            v=edge[u][i].v;
            if(dis[v]==-1 || dis[v]>dis[u]+edge[u][i].w)
            {
                dis[v]=dis[u]+edge[u][i].w;
                if(!vis[v])
                {
                    vis[v]=1;
                    Q.push(v);
                }
            }
        }
    }
}

```

### 传送门

<div class="post-preview">
<a href="/blog/2016/07/29/dijkstra/">
<h4>
Dijkstra最短路算法 - 精讲
</h4>
<h4 class="post-subtitle">
如何理解Dijkstra算法
</h4>
<div class="post-content-preview">
这篇文章通俗易懂 我很喜欢，哈哈哈，推荐新手必看
</div>
</a>
<p class="post-meta">
Posted by 啊哈磊 on July 29, 2016
</p>
</div>
<hr>


<div class="post-preview">
<a href="http://www.cnblogs.com/biyeymyhjob/archive/2012/07/30/2615542.html">
<h4>
Prim算法 - 精讲
</h4>
<h4 class="post-subtitle">
大神请飞过！
</h4>
<div class="post-content-preview">
我是靠这篇博客入门的，开始看算法导论那本板砖一直有点不明白，这篇博客看了就懂啦！
</div>
</a>
<p class="post-meta">
Posted by 华山大师兄 on July 29, 2016
</p>
</div>
<hr>


<div class="post-preview">
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=1863">
<h4>
Prim算法题目 - 畅通工程
</h4>
<h4 class="post-subtitle">
大神不要进！
</h4>
<div class="post-content-preview">
使用Prim算法可以过的，可以考虑用上优化和不优化的对比。
</div>
</a>
<p class="post-meta">
HDU 1863
</p>
</div>
<hr>

<div class="post-preview">
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=1874">
<h4>
Dijkstra算法题目 - 畅通工程续
</h4>
<h4 class="post-subtitle">
可以去试试
</h4>
<div class="post-content-preview">
使用Dijkstra算法可以过的，可以考虑用上优化和不优化的对比。其实还有其他算法可以过得
</div>
</a>
<p class="post-meta">
HDU 1874
</p>
</div>
<hr>

<br><br>


## 另一种更好理解的最小生成树算法——kruskal算法
>这个算法好贪心呀！

### 算法的基本介绍
首先它的中文名字叫做克鲁斯卡尔算法，kruskal算法是通过将每一条边的权从小到大进行排序，排序结束后，从最小的边选起，只要不让选出的边中构成回路就将该边加入树中，知道每个顶点都连通，则算法结束，算法复杂度为 **$$O(elog_{2}e)$$(基于快排)** 。  
明显在稀疏图中采用kruskal算法还可以，时间复杂度不是很高。

### kruskal算法优化
在使用kruskal算法时常常需要检查当前边是否与树会构成回路，如果每次都将树遍历一遍很麻烦，这里我们可以使用并查集（算法导论上叫不相交集合）。  
实现方法如下：  
建立一个数组，数组的编号为节点号，数组内容指向父亲节点。  
实现代码如下：  

```c++
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cstring>
using namespace std;

#define maxn 110    //最多点个数
int n, m;   //点个数，边数
int parent[maxn];   //父亲节点，当值为-1时表示根节点
int ans;    //存放最小生成树权值
struct eage     //边的结构体，u、v为两端点，w为边权值
{
    int u, v, w;
}EG[5010];

bool cmp(eage a, eage b)    //排序调用
{
    return a.w < b.w;
}

int Find(int x)     //寻找根节点，判断是否在同一棵树中的依据
{
    if(parent[x] == -1) return x;
    return Find(parent[x]);
}

void Kruskal()      //Kruskal算法，parent能够还原一棵生成树，或者森林
{
    memset(parent, -1, sizeof(parent));
    sort(EG+1, EG+m+1, cmp);    //按权值将边从小到大排序
    ans = 0;
    for(int i = 1; i <= m; i++)     //按权值从小到大选择边
    {
        int t1 = Find(EG[i].u), t2 = Find(EG[i].v);
        if(t1 != t2)    //若不在同一棵树种则选择该边，合并两棵树
        {
            ans += EG[i].w;
            parent[t1] = t2;
        }
    }
}
int main()
{
    while(~scanf("%d%d", &n,&m))
    {
        for(int i = 1; i <= m; i++)
            scanf("%d%d%d", &EG[i].u, &EG[i].v, &EG[i].w);
        Kruskal();
        printf("%d\n", ans);
    }
    return 0;
}
```

此算法常常用于稀疏图中！
> 提示：稀疏图：边的数量远远小于顶点数量；对应的稠密图就是边的数量比较多，大致有个分界为$${n*(n-1)} / 4$$。

### 传送门

<div class="post-preview">
<a href="http://www.cnblogs.com/biyeymyhjob/archive/2012/07/30/2615542.html">
<h4>
Kruskal算法 - 精讲
</h4>
<h4 class="post-subtitle">
大神请飞过！
</h4>
<div class="post-content-preview">
我是靠这篇博客入门的，开始看算法导论那本板砖一直有点不明白，这篇博客看了就懂啦！
</div>
</a>
<p class="post-meta">
Posted by 华山大师兄 on July 29, 2016
</p>
</div>
<hr>

<div class="post-preview">
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=1863">
<h4>
Kruskal算法题目 - 畅通工程
</h4>
<h4 class="post-subtitle">
大神不要进！
</h4>
<div class="post-content-preview">
使用Kruskal算法可以过的，可以考虑用上优化和不优化的对比。
</div>
</a>
<p class="post-meta">
HDU 1863
</p>
</div>
<hr>

<br><br>


## 单源最短路径——Bellman-ford算法与SPFA算法   
>SPFA算法是使用了队列优化的Bellman-ford算法，是由西南交通大学段凡丁于1994年发表的（中国的，啊哈哈）。

### Bellman-ford算法的自述
这个算法的最大优点是可以求带有负权边的图，这是上面的Dijkstra算法所不能实现的，但不能有负权回路，负权回路会导致算法失效。  
不过这种算与Dijkstra算法共同之处就是都是采用持续松弛的方法不断求得到源点最小权，这一种动态规划思想。  
算法复杂度为 **$$O(v*e)$$** 看起来有点高呀，不过可以适用于更多一般的情况，而且，我们还没有优化呢！可以用斐波那契进行优化。

>由于本专题只讲算法优化与各算法应用情况，所以不再
说明本算法，可以在后面的链接中查看什么是Bellman-ford算法。

### Bellman-ford算法的表哥SPFA算法
SPFA算法就是Bellman-ford算法的大表哥，将Bellman-ford算法进行队列优化后就是我们的SPFA算法啦。  
算法复杂度可以降低到 **$$O(k*e)(k<=2)$$** 效率变高了有木有！
实现方法：  
1. 在同所有的单源最短路径算法一样，初始化源点到各个点的权，不直接连通则为最大值。
2. 进行松弛操作。在松弛操作时借助队列来优化，读取队列头部元素为当前顶点并将队列头部元素出队，将该顶点连通的点
进行松弛操作，如能让当前权所在数组中的值变小则更新，并且当进行松弛操作时当前节点不在队列中，则将
则将该节点加入队列中，空队列是算法结束的标志（如果有负权回路会挂的,所以最好加上判断一个顶点加入队列的次数来解决这个问题。）

有两个版本使用bfs或者dfs，bfs好理解一些，dfs不怎么好理解但判断负环稳定，求大神教我dfs版的。
代码实现如下：  

```c++
//BFS版本，判断负环不稳定，此为通用模板。
#include <queue>
#include <cstdio>

int n;  //表示n个点，从1到n标号
int s,t;  //s为源点，t为终点
int d[N];  //d[i]表示源点s到点i的最短路
int p[N];  //记录路径（或者说记录前驱）
queue <int> q;  //一个队列，用STL实现，当然可有手打队列，无所谓
bool vis[N];   //vis[i]=1表示点i在队列中 vis[i]=0表示不在队列中

int spfa(int s)
{
    queue <int> q;
    memset(d,0x3f,sizeof(d));
    d[s]=0;
    memset(c,0,sizeof(c));
    memset(vis,0,sizeof(vis));
    q.push(s);  vis[s]=1; c[s]=1;
    //顶点入队vis要做标记，另外要统计顶点的入队次数
    int OK=1;
    while(!q.empty())
    {
        int x;
        x=q.front(); q.pop();  vis[x]=0;
        //队头元素出队，并且消除标记
        for(int k=f[x]; k!=0; k=nnext[k]) //遍历顶点x的邻接表
        {
            int y=v[k];
            if( d[x]+w[k] < d[y])
            {
                d[y]=d[x]+w[k];  //松弛
                if(!vis[y])  //顶点y不在队内
                {
                    vis[y]=1;    //标记
                    c[y]++;      //统计次数
                    q.push(y);   //入队
                    if(c[y]>NN)  //超过入队次数上限，说明有负环
                        return OK=0;
                }
            }
        }
    }
    return OK;
}
```

```c++
//DFS版本，判断负环稳定，此为通用模板。
#include <queue>
#include <cstdio>

int n;  //表示n个点，从1到n标号
int s,t;  //s为源点，t为终点
int d[N];  //d[i]表示源点s到点i的最短路
int p[N];  //记录路径（或者说记录前驱）
queue <int> q;  //一个队列，用STL实现，当然可有手打队列，无所谓
bool vis[N];   //vis[i]=1表示点i在队列中 vis[i]=0表示不在队列中

int spfa(int u)
{
    vis[u]=1;
    for(int k=f[u]; k!=0; k=e[k].next)
    {
        int v=e[k].v,w=e[k].w;
        if( d[u]+w < d[v] )
        {
            d[v]=d[u]+w;
            if(!vis[v])
            {
                if(spfa(v))
                    return 1;
            }
            else
                return 1;
        }
    }
    vis[u]=0;
    return 0;
}
```

### 传送门

<div class="post-preview">
<a href="http://www.cnblogs.com/biyeymyhjob/archive/2012/07/30/2615542.html">
<h4>
Bellman-ford算法 - 精讲
</h4>
<h4 class="post-subtitle">
大神请飞过！
</h4>
<div class="post-content-preview">
这篇博客看起来有点不好懂，但是关键有图，应该不会很难的。
</div>
</a>
<p class="post-meta">
Posted by 匠心十年 on July 29, 2016
</p>
</div>
<hr>

<div class="post-preview">
<a href="http://blog.sina.com.cn/s/blog_a46817ff01015g9h.html">
<h4>
SPFA算法 - 精讲
</h4>
<h4 class="post-subtitle">
大神请飞过！
</h4>
<div class="post-content-preview">
虽然没有图，但还是通俗易懂的。
</div>
</a>
<p class="post-meta">
Posted by 疯狂之桥 on July 29, 2016
</p>
</div>
<hr>

<div class="post-preview">
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=2544">
<h4>
SPFA算法题目 - 最短路
</h4>
<h4 class="post-subtitle">
大神不要进！
</h4>
<div class="post-content-preview">
使用SPFA算法可以过的，试一下有没有其他办法！
</div>
</a>
<p class="post-meta">
HDU 1863
</p>
</div>
<hr>

<br><br>


## 多源最短路径算法——Floyd算法
> 你们还在求一个点吗，我已经求出所有点之间的最短路径了！

### 算法自述
这个算法比较强大的地方就是在于，可以计算任意两点的最短路径，可以有负权，但不能是有负环的。缺点就是算法的时间复杂度高达 **$$O(n^3)$$**，数据量一大，就会非常缓慢.  
这个算法也是利用了动态规划的思想，但是怎么看着都有点像离散数学中学到的用Warshall来计算传递闭包，感觉好熟悉。  

### 算法的优化
看了很多关于这个算法的优化方法，大多是根据实际情况来优化的，所以没有一种通用的优化。

### 传送门

<div class="post-preview">
<a href="/blog/2016/07/29/floyd/">
<h4>
Floyd最短路算法 - 精讲
</h4>
<h4 class="post-subtitle">
如何理解Floyd算法
</h4>
<div class="post-content-preview">
这篇文章通俗易懂 我很喜欢，哈哈哈，推荐新手必看！
</div>
</a>
<p class="post-meta">
Posted by 啊哈磊 on July 29, 2016
</p>
</div>
<hr>

<div class="post-preview">
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=2066">
<h4>
Floyd算法题目 - 一个人的旅行
</h4>
<h4 class="post-subtitle">
大神不要进！
</h4>
<div class="post-content-preview">
使用Floyd算法可以过的，当然，你还可以试试其他算法解题。
</div>
</a>
<p class="post-meta">
HDU 1863
</p>
</div>
<hr>

<br><br>


## 各种算法的比较
> 到了最后 总是需要一个表来帮助我们直观的看一下各个算法

### 最小生成树

比较类型 | Kruskal算法 | Prim算法 | Kruskal算法(优化后) | Prim算法(优化后)
-----| ------- | ------ | ------- | ------
算法复杂度| ？ | $$O(v^2)$$ | $$O(elog_{2}v)$$ | $$O(elog_{2}v)$$
是否可以负权| 可以 | 可以 | 可以 | 可以
稀疏图时 | 效率较高 | 效率较低 | 效率更高 | 效率较高    
稠密图时 | 效率低 | 效率较高 | 效率较低 | 效率较高
实现难度 | 低 | 中 | 中 | 较高

### 最短路径

比较类型 | SPFA算法 | Dijkstra算法 | Dijkstra算法(优化后) | Floyd算法
-----| ------- | ------ | ------- | -------
算法复杂度| $$O(k*e)(k<=2)$$ | $$O(v^2)$$ | $$O(elog_{2}v)$$ | $$O(n^3)$$
是否可以负权 | 可以 | 不可以 | 不可以 | 可以
实现难度 | 中 | 较低 | 中 |  低
是否多源 | 单源 | 单源 | 单源 | 多源

---

{% if site.duoshuo_username %}
<!-- 多说评论框 start -->
<div class="comment">
    <div class="ds-thread"
        data-thread-key="{{page.id}}"
        data-title="{{page.title}}"
        data-url="{{site.url}}{{site.baseurl}}{{page.url}}" >
    </div>
</div>
<!-- 多说评论框 end -->
{% endif %}


{% if site.duoshuo_username %}
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
    // dynamic User by Hux
    var _user = '{{site.duoshuo_username}}';

    // duoshuo comment query.
    var duoshuoQuery = {short_name: _user };
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0]
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
</script>
<!-- 多说公共JS代码 end -->
{% endif %}


{% if site.disqus_username %}
<!-- disqus 公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES * * */
    var disqus_shortname = "{{site.disqus_username}}";
    var disqus_identifier = "{{page.id}}";
    var disqus_url = "{{site.url}}{{page.url}}";

    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<!-- disqus 公共JS代码 end -->
{% endif %}


{% if site.anchorjs %}
<!-- async load function -->
<script>
    function async(u, c) {
      var d = document, t = 'script',
          o = d.createElement(t),
          s = d.getElementsByTagName(t)[0];
      o.src = u;
      if (c) { o.addEventListener('load', function (e) { c(null, e); }, false); }
      s.parentNode.insertBefore(o, s);
    }
</script>
<!-- anchor-js, Doc:http://bryanbraun.github.io/anchorjs/ -->
<script>
    async("http://cdn.bootcss.com/anchor-js/1.1.1/anchor.min.js",function(){
        anchors.options = {
          visible: 'always',
          placement: 'right',
          icon: ''
        };
        anchors.add().remove('.intro-header h1').remove('.subheading').remove('.sidebar-container h5');
    })
</script>
<style>
    /* place left on bigger screen */
    @media all and (min-width: 800px) {
        .anchorjs-link{
            position: absolute;
            left: -0.75em;
            font-size: 1.1em;
            margin-top : -0.1em;
        }
    }
</style>
{% endif %}
