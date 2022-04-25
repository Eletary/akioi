# 并查集

并查集是一种维护不相交集合的数据结构。

## 简介

并查集维护一颗森林，其中在每一棵树构成一个集合。规定每棵树的根的父亲为它自己。

并查集的操作有两种：

- $\text{Find}(x)$ 查找 $x$ 的祖先，如果两个元素祖先相同则它们在同一个集合里
- $\text{Union}(x,y)$ 又称 $\text{merge}$，将 $x$ 和 $y$ 所在的集合合并（令 $Find(x)$ 的祖先为 $\text{Find}(y)$）

```cpp
int Find(int x) {return x==f[x]?x:Find(f[x]);}
void merge(int x,int y) {f[Find(x)]=Find(y);}
```

在树退化成链时，每次操作为 $\Theta(n^2)$。总体复杂度 $O(n^2)$。

## 优化

### 按秩合并

按照 $size$ 或 $depth$ 进行启发式合并，将秩小的合并到秩大的上面。

一般 $size$ 结合路径压缩使用，$depth$ 单独使用。

一般用于不能路径压缩或压缩代价极大的题目。

### 路径压缩

```cpp
int Find(int x) {return x==f[x]?x:f[x]=Find(f[x]);}
```
$O(\alpha(n))$。几乎 $=O(1)$。

## 带权并查集

### 查询

带权并查集，即在从 $a$ 到 $b$ 的边上维护一个权值 $f(a,b)$。该权值需要满足：

- 由 $f(a,b)$ 和 $f(a,c)$ 可以推导出 $f(b,c)$
- 且 $f(a,b)$ 可以推导出 $f(b,a)$。
- $f(a,a)=0$

这样的话，点 $a$ 上维护 $f(a,\text{Find}(a))$ 即可。若 $a$ 和 $b$ 在一个集合内，则因为已知 $f(a,rt)$ 和 $f(b,rt)$，所以可以得出 $f(a,b)$。

如此，每一个集合内点之间两两的权值都是已知的。

### 合并

已知 $f(a,b)$。要将 $a$ 所在的集合合并到 $b$ 上。此时每个点存的权值更新为 $f(x,\text{Find}(b))$。

显然只要求出 $f(\text{Find}(a),\text{Find}(b))$ 即可（对于 $a$ 所在的集合内的任意点 $x$，$f(x,\text{Find}(a))$ 和 $f(\text{Find(a)},\text{Find(b)})$ 已知）。

因为 $f(a,\text{Find}(a)),f(a,b))$ 已知，所以可知 $f(b,\text{Find(a)})。$ y又已知 $f(b,\text{Find}(b))$ 所以可以求出。

至此：只剩下一个问题：更新 $a$ 所在的集合内点的权值。

### 路径压缩（延迟更新）

我们可以在压缩时更新。

首先压缩父亲，此时树上应该是 $a \to fa \to rt$。

显然已知 $f(a,fa),f(fa,rt)$。

结束了。

### 例题

一般这类题目都会给一堆两点之间的关系。

> 给定 $m$ 组关系 $a_x-a_y=c$，判断是否造假（后面的关系和前面冲突）。

将 $f(a,b)$ 替换为 $a-b$ 即可。
$$
f(b,a)=f(a,c)-f(a,b)\\
f(b,a)=-f(a,b)\\
f(a,a)=a-a=0
$$

三条性质满足，直接上代码就行了。

```cpp
void collapse(int x) // 路径压缩
{
    if (x==f[x]) return;
    collapse(f[x]);
    dis[x]+=dis[f[x]]; // (x-fa)+(fa-rt)=x-rt
    f[x]=f[f[x]];
}
int Find(int x) {collapse(x);return f[x];}
bool Union(int x,int y,int z) // 若造假返回 true，其余情况返回 false
{
    if (Find(x)==Find(y)) return dis[x]-dis[y]!=z; // (x-rt)-(y-rt)=x-y
    dis[f[x]]=z-dis[x]+dis[y]; // (x-y)-(x-Fx)+(y-Fy)=Fx-Fy;
    f[f[x]]=f[y];
    return false;
}
```

---

有时带权并查集不能直接维护目标信息：

> 给定 $m$ 组关系 $a_x+a_y=c\ \ (1\leq x\leq n,n<y\leq 2n)$，判断是否造假（后面的关系和前面冲突）。

$a+b$ 不好直接维护。但是本题有一种有趣的性质： $a$ 和 $b$ 是不相交的，所以可以认为维护信息为 $a-(-b)$。这样，要维护/查询的信息就总是 $x-y$。

---

有时候带权并查集结合其他信息维护：

> 有 $n$ 个小于 $2^{20}$ 的数：
> - 告诉你 $X_p=v$
> - 告诉你 $X_p\oplus X_q=v$
> - 给定 $k$ 个下标，求这些下标对应值的异或和

同时维护一个 $val$。对于每次操作 1，令 $val(rt):=v\oplus dis(p)$。合并时两集合任意一边根有值则令新根有值。查询时先排掉有值的，剩下的两两配对即可（对于在同一集合的 $p,q$，$X_p\oplus X_q=dis(p)\oplus dis(q)$）。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN{20000};
int f[MAXN+5],dis[MAXN+5];
int val[MAXN+5];
void collapse(int x)
{
    if (x==f[x]) return;
    crupt(f[x]);
    dis[x]^=dis[f[x]];
    f[x]=f[f[x]];
}
int getf(int x){collapse(x);return f[x];}
int getv(int x)
{
    collapse(x);
    if (val[f[x]]!=-1) val[x]=dis[x]^val[f[x]];
    return val[x];
}
void setv(int x,int y)
{
    if (getv(x)!=-1) return;
    val[x]=y;
    val[f[x]]=dis[x]^val[x];
}
void merge(int a,int b,int x)
{
    if (getf(a)==getf(b)) return;
    if (getv(a)!=-1&&getv(b)!=-1) return;
    dis[f[a]]=dis[a]^dis[b]^x;
    if (val[f[a]]!=-1) val[f[b]]=val[f[a]]^dis[f[a]];
    f[f[a]]=f[b];
}
int qwq[MAXN+5];
int main()
{
    int n,q;cin>>n>>q;
    for (int i{1};i<=n;++i) f[i]=i,dis[i]=0,val[i]=-1;
    while (q--)
    {
        int op,p;scanf("%d %d",&op,&p);
        if (op==1)
        {
            int v;scanf("%d",&v);
            setv(p+1,v);
        }
        else if (op==2)
        {
            int q,v;scanf("%d %d",&q,&v);
            merge(p+1,q+1,v);
        }
        else
        {
            int k[15];
            int cnt{0},sum{0};
            for (int i{0};i<p;++i)
            {
                int w;scanf("%d",&w);++w;
                if (getv(w)!=-1) sum^=val[w];
                else k[cnt++]=w;
            }
            for (int i{0};i<cnt;++i) qwq[getf(k[i])]=0;
            for (int i{0};i<cnt;++i)
            {
                if (qwq[f[k[i]]]) sum^=dis[qwq[f[k[i]]]]^dis[k[i]],qwq[f[k[i]]]=0;
                else qwq[f[k[i]]]=k[i];
            }
            for (int i{0};i<cnt;++i)
                if (qwq[f[k[i]]])
                {
                    printf("I don't know.\n");
                    goto xun;
                }
            printf("%d\n",sum);
            xun:;
        }
    }
    return 0;
}
```