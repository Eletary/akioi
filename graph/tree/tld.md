# 树链剖分

树链剖分一般指重链剖分。

树剖按照指定特殊儿子的方式将树剖成一些链。

## 重链剖分

重剖令 `size` 最大的儿子为重儿子。向重儿子连重边，其他儿子连轻边。

因为每走到一条轻边，子树的 `size` 至少减半，所以每个结点到根最多有 $\lg n$ 条轻边。

### 求 LCA

树剖核心。

每次选取链顶更深的儿子向上跳即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N(3e5);
int sz[N+5],dep[N+5],fa[N+5];
int tp[N+5],son[N+5];
vector<int> e[N+5];
void fz_init(int u,int fat)
{
    fa[u]=fat;dep[u]=dep[fat]+1;sz[u]=1;
    for (int v:e[u])
    {
        if (v==fat) continue;
        fz_init(v,u);
        sz[u]+=sz[v];
        if (sz[v]>sz[son[u]])
            son[u]=v;
    }
}
void fz_cut(int u,int top)
{
    tp[u]=top;
    if (son[u]==0) return;
    fz_cut(son[u],top);
    for (auto v:e[u])
    {
        if (v==fa[u]||v==son[u]) continue;
        fz_cut(v,v);
    }
}
int LCA(int a,int b)
{
    while (tp[a]!=tp[b])
    {
        if (dep[tp[a]]<dep[tp[b]]) b=fa[tp[b]];
        else a=fa[tp[a]];
    }
    if (dep[a]>dep[b]) swap(a,b);
    return a;
}
int main()
{
    int n,m,s{1};cin>>n>>m;
    for (int i{1};i<n;++i)
    {
        int a,b;scanf("%d %d",&a,&b);
        e[a].push_back(b);
        e[b].push_back(a);
    }
    fz_init(s,0);fz_cut(s,s);
    while (m--)
    {
        int a,b;scanf("%d %d",&a,&b);
        printf("%d\n",LCA(a,b));
    }
    return 0;
}
```

### 求解其他问题

类似 LCA。重链在 dfs 序连续，类似 LCA 向上跳即可。

以一个 $\lg$ 的代价转化为区间问题。