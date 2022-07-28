# 树上启发式合并

离线，能够处理任何扫描每个点的子树（$\Theta(n^2)$）解决的问题可以转化为 dsu on tree $\Theta(n\lg n)$。

> 求树上有多少条长度为 $k$ 的路径。

```cpp
#include <bits/stdc++.h>
using namespace std;
inline int read() {
    char c;int x;
    do x=(c=getchar())^48;
    while (!isdigit(c));
    while (isdigit(c=getchar()))
        x=(x<<3)+(x<<1)+(c^48);
    return x;
}
const int N(1e5);
int cnt[100*N+5];
int dfn[N+5],fz[N+5],sz[N+5],son[N+5];
int R[N+5],a[N+5],K;
long long ans;
inline void ins(int u){++cnt[a[u]];}
vector<pair<int,int>> e[N+5];
void fz_init(int u,int fa) {
    static int cxx{1};
    fz[cxx]=u;dfn[u]=cxx++;
    sz[u]=1;
    for (auto x:e[u]) {
        int v{x.first},w{x.second};
        if (v!=fa) {
            a[v]=a[u]+w;
            fz_init(v,u);
            sz[u]+=sz[v];
            if (sz[v]>sz[son[u]])
                son[u]=v;
        }
    }
    R[u]=cxx;
}
void fz_dsu(int u,int fa) {
    for (auto x:e[u]) {
        int v{x.first};
        if (v!=son[u]&&v!=fa) {
            fz_dsu(v,u);
            for (int i{dfn[v]};i!=R[v];++i)
                cnt[a[fz[i]]]=0;
        }
    }
    if (son[u]) fz_dsu(son[u],u);
    for (auto x:e[u]) {
        int v{x.first};
        if (v!=son[u]&&v!=fa) {
            for (int i{dfn[v]};i!=R[v];++i)
                if (a[fz[i]]-a[u]<=K)
                    ans+=cnt[a[u]*2+K-a[fz[i]]];
            for (int i{dfn[v]};i!=R[v];++i)
                ins(fz[i]);
        }
    }
    ans+=cnt[K+a[u]];
    ins(u);
}
int main()
{
    int n{read()};K=read();
    for (int i{1};i<n;++i) {
        int a{read()},b{read()},c{read()};
        e[a].emplace_back(b,c);
        e[b].emplace_back(a,c);
    }
    fz_init(1,0);fz_dsu(1,0);
    cout<<ans<<endl;
    return 0;
}
```