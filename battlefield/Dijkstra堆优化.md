## 关于单源最短路Dijkstra的堆优化

**题意：寻找从某个点到其他所有点的最短路径[洛谷P4779](https://www.luogu.com.cn/problem/P4779)**

*教程[OIWiki](https://oi-wiki.org/graph/shortest-path/#dijkstra-%E7%AE%97%E6%B3%95)*

#### 朴素Dijkstra

由常规的dij算法可以得知，每次都遍历该点的所有出点，然后对该点的这些出点进行松弛操作
即

~~~
if(d[x]+w<d[y])
    d[y]=d[x]+w;
~~~

借此来实现每一个点对其他所有点的松弛操作

但是，这样的时间复杂度为 $O(n^2)$ ，显然不是很优秀

*
PS：存图方式为终点+边权式
~~~
struct edge
{
    int y,w;
};
vector<edge> g[N] //N->x;
~~~
*

#### 堆优化

在模拟松弛时的行进路线不难看出，有很多点的松弛操作是没必要进行的，比如扫到了一个本就很大的路径，而表内已经存在较小的数据。

同时可以发现，编号扫描的先后性并不会改变<这条路就是最短路>这一事实，所以此处可以进行优化

既然要每次都选择该点连接的最短的路径，很容易想到一个数据结构：`小根堆`

所以我们就可以进行堆优化

**对于堆优化**

前期使用的 `init()` 和存图方式与朴素版几乎相同

* 小根堆的建立

    使用仿函数模拟cmp
    
    ~~~
    struct cmp
    {
        bool operator()(const edge &a,const edge &b)
        {
            return a.w==b.w?a.y>b.y:a.w>b.w;
        }
    };
    priority_queue <edge,vector<int>,cmp> pq;
    ~~~
    
* dij的实现

    首先将起始点导入到空堆中
    
    然后遍历堆直到堆为空，即遍历所有有意义的边
    
    对于每条边的出点，若该点目前正在被访问，证明该点之前已经是最优解（小根堆的特性实现贪心），故若该点已经被访问，直接 `continue` 
    
    然后遍历该点的所有出点，将可以松弛的放入小根堆，不必松弛的便没有进行其他访问的意义
    即
    ~~~
    for(const auto &t:g[x])
    {
        int y=t.y,w=t.w;
        if(d[x]+w<d[y])
        {
            d[y]=d[x]+w;
            pq.push({y,d[y]});
        }
    }
    ~~~
    
* 至此，所有d[x]均已松弛完毕

附完整代码

~~~
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
#define tb ios::sync_with_stdio(false),cin.tie(0),cout.tie(0)
const int inf=1e9;

const int N=1e5+9;
int n,m,s;
struct edge
{
    int y,w;
};
vector<edge> g[N];
struct cmp
{
    bool operator()(const edge &a,const edge &b)
    {
        return a.w==b.w?a.y>b.y:a.w>b.w;
    }
};
priority_queue<edge,vector<edge>,cmp> pq;
bitset<N> vis;
int d[N];

void dij(int k)
{
    for(int i=1;i<=n;++i)
        d[i]=inf;
    d[k]=0;
    pq.push({k,d[k]});
    while(!pq.empty())
    {
        int x=pq.top().y;
        pq.pop();
        if(vis[x])
            continue;
        vis[x]=1;
        for(const auto &t:g[x])
        {
            int y=t.y,w=t.w;
            if(d[x]+w<d[y])
            {
                d[y]=d[x]+w;
                pq.push({y,d[y]});
            }
        }
    }
}

int main()
{
    tb;
    cin>>n>>m>>s;
    for(int i=1;i<=m;++i)
    {
        int u,v,w;
        cin>>u>>v>>w;
        g[u].push_back({v,w});
    }
    dij(s);
    for(int i=1;i<=n;++i)
        cout<<d[i]<<' ';
    return 0;
}
~~~
