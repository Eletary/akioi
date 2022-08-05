# A*

嗨嗨嗨，我有紫书了！

记录个 K 短路代码。

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
const int N{1000},M(1e4);
int dis[N+5];
bool vis[N+5];int g[N+5];
int head[N+5],nxt[M+5];
int U[M+5],V[M+5],W[M+5];
int n,m,cnt;
struct node {
    int x,dis;
    friend bool operator<(node a,node b){
        return g[a.x]+a.dis<g[b.x]+b.dis||g[a.x]+a.dis==g[b.x]+b.dis&&a.dis<b.dis;
    }
    friend bool operator>(node a,node b){
        return b<a;
    }
    node(int _x,int _dis):x{_x},dis{_dis}{}
};
void addE(int u,int v,int w) {
    ++cnt;
    U[cnt]=u;V[cnt]=v;W[cnt]=w;
    nxt[cnt]=head[u];head[u]=cnt;
}
void SPA(int u) {
    memset(g,0x3f,sizeof g);
    g[u]=0;
    for (int i{1};i<n;++i)
        for (int j{1};j<=m;++j)
            g[U[j]]=min(g[U[j]],g[V[j]]+W[j]);
}
int main() {
    n=read();m=read();
    int k{read()};
    for (int i{1};i<=m;++i) {
        int u{read()},v{read()},w{read()};
        addE(u,v,w);
    }
    SPA(1);
    priority_queue<node,vector<node>,greater<node>> pq;
    pq.emplace(n,0);
    while (pq.size()) {
        auto p{pq.top()};pq.pop();
        if (p.x==1) {
            printf("%d\n",p.dis);
            if (--k==0) break;
        }
        for (int x{head[p.x]};x;x=nxt[x])
            pq.emplace(V[x],p.dis+W[x]);
    }
    while (k--) printf("-1\n");
    return 0;
}
```