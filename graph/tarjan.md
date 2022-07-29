# Tarjan 图论算法

## 割点 & 割边

Tarjan 板子记录。

如果子树的 `low` 大于等于当前结点就是割点，根结点特判。

割边就更简单了。

```cpp
// 割点
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
const int N(2e4);
vector<int> e[N+5];
int dfn[N+5],low[N+5];
bool flg[N+5];int cxx;
void trajan(int u,int fa) {
    dfn[u]=low[u]=cxx++;
    int son{0};
    for (auto v:e[u]) {
        if (v==fa) continue;
        if (dfn[v]) {
            low[u]=min(low[u],dfn[v]);
        } else {
            trajan(v,u);
            low[u]=min(low[u],low[v]);
            if (fa&&low[v]>=dfn[u]||!fa&&++son>=2) flg[u]=true;
        }
    }
}
int main() {
    int n{read()},m{read()};
    for (int i{1};i<=m;++i) {
        int u{read()},v{read()};
        e[u].push_back(v);
        e[v].push_back(u);
    }
    for (int i{1};i<=n;++i)
        if (!dfn[i]) {
            cxx=1;
            trajan(i,0);
        }
    int ans{0};
    for (int i{1};i<=n;++i)
        ans+=flg[i];
    cout<<ans<<endl;
    for (int i{1};i<=n;++i)
        if (flg[i])
            printf("%d ",i);
    return 0;
}
```

用于处理有向图也类似（强连通）。

以下为缩点的代码。

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
const int N(1e4);
int w[N+5],w2[N+5];
vector<int> e[N+5],e2[N+5],re[N+5];
int dfn[N+5],low[N+5],scc[N+5],ind[N+5];
bool ins[N+5];
stack<int> st;
int cxx,cy;
void trajan(int u) {
    dfn[u]=low[u]=cxx++;
    st.push(u);ins[u]=true;
    for (auto v:e2[u]) {
        if (dfn[v]) {
            if (ins[v])
                low[u]=min(low[u],dfn[v]);
        } else {
            trajan(v);
            low[u]=min(low[u],low[v]);
        }
    }
    if (low[u]==dfn[u]) {
        ++cy;
        if (st.top()!=u) e[0].push_back(cy);
        st.push(0);
        do {
            st.pop();
            ins[st.top()]=false;
            scc[st.top()]=cy;
            w[cy]+=w2[st.top()];
        } while (st.top()!=u);
        st.pop();
    }
}
int f[N+5];
int main() {
    int n{read()},m{read()};
    for (int i{1};i<=n;++i)
        w2[i]=read();
    for (int i{1};i<=m;++i)
        e2[read()].push_back(read());
    for (int i{1};i<=n;++i) 
        if (!dfn[i]) {
            cxx=1;
            trajan(i);
        }
    for (int u{1};u<=n;++u)
        for (auto v:e2[u])
            if (scc[u]!=scc[v]) {
                e[scc[u]].push_back(scc[v]);
                re[scc[v]].push_back(scc[u]);
                ++ind[scc[v]];
            }
    queue<int> q;
    for (int i{1};i<=cy;++i)
        if (!ind[i])
            q.push(i);
    cxx=1;
    while (q.size()) {
        int u{q.front()};q.pop();
        dfn[u]=cxx++;
        for (auto v:e[u]) {
            if (!--ind[v])
                q.push(v);
        }
    }
    int ans{0};
    for (int i{1};i<=cxx;++i) {
        for (auto v:e[i])
            f[i]=max(f[i],f[v]);
        ans=max(ans,f[i]+=w[i]);
    }
    cout<<ans<<endl;
    return 0;
}
```