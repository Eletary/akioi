# 块状数组

块状数组是一种常用的数据结构。

可以认为块状数组维护一棵深度为 $2$ 的线段树（根结点深度为 $0$），一般令块长为 $\sqrt{n}$。

线段树能做的事情块状数组基本都能做。

## 序列分块

对序列进行分块，每一块维护一些信息。一般用于做区间操作。

> - 区间加
> - 区间乘
> - 查询区间和

```cpp
// 70 pts TLE 代码，有时间改
const int N(1e5),B(800),K(N/B+1);
int L[B],R[B],pro[B],add[B],sum[B];
int a[N];
int b6e0;
inline int tag(int x,int p,int s){return (1LL*x*p%b6e0+s)%b6e0;}
void construct(int l,int r,int p,int s)
{
    for (int i{L[l/K]};i<=R[r/K];++i)
        a[i]=tag(a[i],pro[i/K],add[i/K]);
    for (int i{l/K};i<=r/K;++i)
        pro[i]=1,add[i]=0,sum[i]=0;
    for (int i{l};i<=r;++i)
        a[i]=tag(a[i],p,s);
    for (int i{L[l/K]};i<=R[r/K];++i)
        sum[i/K]=(sum[i/K]+a[i])%b6e0;
}
int search(int l,int r)
{
    int ans{0};
    for (int i{l};i<=r;++i)
        ans=(ans+tag(a[i],pro[i/K],add[i/K]))%b6e0;
    return ans;
}
void multip(int l,int r,int p=1,int s=0)
{
    if (l/K==r/K) construct(l,r,p,s);
    else
    {
        construct(l,R[l/K],p,s);
        construct(L[r/K],r,p,s);
        for (int i{l/K+1};i<r/K;++i)
            pro[i]=tag(pro[i],p,0),add[i]=tag(add[i],p,s);
    }
}
int query(int l,int r)
{
    if (l/K==r/K) return search(l,r);
    else
    {
        int ans{(search(l,R[l/K])+search(L[r/K],r))%b6e0};
        for (int i{l/K+1};i<r/K;++i)
            ans=(ans+tag(sum[i],pro[i],1LL*(R[i]-L[i]+1)*add[i]%b6e0))%b6e0;
        return ans;
    }
}
```

块内有可能维护更多的奇怪信息。

> - 区间加
> - 查询区间小于 $k$ 的数个数

块内维护这个块的有序版本。修改整块打标记，散块重构；查询整块块二分，散块暴力统计。

时间复杂度 $O(m \max(B+\frac{n}{B},B\lg(\frac{n}{B})+\frac{n}{B}))$，当 $B=\Theta(\sqrt{n\lg n})$ 最优，为 $O(m\sqrt{n\lg n})$。

## 值域分块

类似权值线段树。

很遗憾，动态开点块状数组的空间复杂度一般为 $O(min(m\sqrt{n},n))$，仍然需要离散化，所以该项技术未被应用。