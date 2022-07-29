# 生成树

1. 最小生成树保证每条路径上的最大值最小
1. 生成树可以将图分成树边与非树边，类似缩点操作的效果

> 给定一个n个点m条边的无向图，求删除某条之后的最小生成树。

跑一遍最小生成树。对于非树边显然答案不变。

同时，非树边 $(u,v)$ 可以在删掉树上路径 $u$ 到 $v$ 中的任意一条边时替换它，所以对于每条非树边，我们去更新链上被替换后的 $\Delta$。

注意到被小的边更新后不可能被大的非树边更新，考虑对边从小到大排序更新，同时使用并查集合并掉被更新的边。

显然树链的时候 LCA 暴力跑即可。

复杂度 $\Theta(m\lg m)$，瓶颈求最小生成树。

```cpp
#include <bits/stdc++.h>
using namespace std;
inline int read() {
    char c;int x,f{0};
    do x=(c=getchar())^48;
    while (!isdigit(c)&&c!='-');
    if (x==29) x=0,f=-1;
    while (isdigit(c=getchar()))
        x=(x<<3)+(x<<1)+(c^48);
    return (x^f)-f;
}
const int N(5e4),M(2e5);
struct EE {
    int v,id;
    EE(int _v,int _id):v{_v},id{_id}{}
};
vector<EE> e[N+5];
struct E {
    int u,v,w,id;
    bool in;
    friend bool operator<(E a,E b){return a.w<b.w;}
} edge[M+5];
int f[N+5],fa[N+5],dep[N+5],ID[N+5];
int Find(int x){return x==f[x]?x:f[x]=Find(f[x]);}
bool merge(int u,int v){return Find(u)!=Find(v)&&(f[f[u]]=f[v]);}
void fz_init(int u,int f) {
    fa[u]=f;dep[u]=dep[f]+1;
    for (auto v:e[u])
        if (v.v!=f)
            fz_init(v.v,u);
        else ID[u]=v.id;
}
int ans[M+5];
int main() {
    int n{read()},m{read()};
    for (int i{1};i<=m;++i) {
        edge[i].u=read();
        edge[i].v=read();
        edge[i].w=read();
        edge[i].id=i;
    }
    sort(edge+1,edge+m+1);
    for (int i{1};i<=n;++i)
        f[i]=i;
    int cnt{0};
    for (int i{1};i<=m;++i) {
        if (merge(edge[i].u,edge[i].v)) {
            cnt+=edge[i].w;edge[i].in=true;
            e[edge[i].u].emplace_back(edge[i].v,i);
            e[edge[i].v].emplace_back(edge[i].u,i);
        }
    }
    memset(ans,-1,sizeof ans);
    fz_init(1,1);
    for (int i{1};i<=n;++i) f[i]=i;
    for (int i{1};i<=m;++i) {
        if (!edge[i].in) {
            ans[edge[i].id]=cnt;
            int u{Find(edge[i].u)},v{Find(edge[i].v)};
            while (u!=v) {
                if (dep[u]<dep[v]) swap(u,v);
                int e{ID[u]};
                ans[edge[e].id]=edge[i].w-edge[e].w+cnt;
                u=f[u]=Find(fa[u]);
            }
        }
    }
    for (int i{1};i<=m;++i)
        printf("%d\n",ans[i]);
    return 0;
}
```