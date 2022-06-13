# 线段树

## 简介

线段树是一种利用分治维护区间的数据结构。

线段树是一棵伪完全二叉树（底层叶子结点不保证从左向右），每一个叶结点维护单位区间（序列上为一个元素），其他结点则维护两儿子信息的合并。
相当于就是一棵分治树。

## 建树

用指针简单处理下就行了。

```cpp
struct tnode
{
    int L,R;
    tnode *l,*r;
    int mx;
    void upd() {mx=max(l->mx,r->mx);}
};
int a[MAXN];
void build(tnode* p,int L,int R)
{
    p->L=L;p->R=R;
    if (L==R-1)
    {
        p->mx=a[L];
        return;
    }
    int mid{L+R>>1};
    build(p->l=new tnode,L,mid);
    build(p->r=new tnode,mid,R);
    p->upd();
}
```

## 区间操作

> - 区间赋值
> - 查询区间最大子段和（不能不选）

经典线段树应用。

```cpp
struct ran
{
    long long sum,ls,rs,ms;
    ran& operator=(long long x) {sum=ls=rs=ms=x;return *this;}
    ran(long long _s,long long _l,long long _r,long long _m):sum{_s},ls{_l},rs{_r},ms{_m} {};
    ran(long long x) {*this=x;}
    ran():ran(0,0,0,0) {}
};
ran operator+(const ran& a,const ran& b) {
    return
    {
        a.sum+b.sum,
        max(a.ls,a.sum+b.ls),
        max(b.rs,b.sum+a.rs),
        max(max(a.ms,b.ms),a.rs+b.ls)
    };
}
struct tnode
{
    using pnode=tnode*;
    long long L,R;
    pnode l,r;
    ran d;long long set;
    void upd() {d=l->d+r->d;}
    void dld() {if(set==-1)return;l->tag(set);r->tag(set);set=-1;}
    void tag(long long x) {d=x;}
};
using pnode=tnode::pnode;
const long long MAXN(1e5+5);
long long a[MAXN];
void build(pnode p,long long L,long long R)
{
    p->L=L;p->R=R;p->d=0;p->set=-1;
    if (L==R-1) return p->tag(a[L]);
    long long mid{L+R>>1};
    build(p->l=new tnode,L,mid);
    build(p->r=new tnode,mid,R);
    upd(p);
}
void modify(pnode p,long long L,long long R.long long x)
{
    if (L<=p->L&&p->R<=R) return p->tag(x);
    if (R<=p->L||p->R<=L) return;
    p->dld();
    add(p->l,k,x);add(p->r,k,x);
    p->upd();
}
ran query(pnode p,long long L,long long R)
{
    if (L<=p->L&&p->R<=R) return p->d;
    if (R<=p->L||p->R<=L) return INT_MIN;
    p->dld();
    return query(p->l,L,R)+query(p->r,L,R);
}
```

## 权值线段树

> 如果一个序列，它的任意两个相邻的元素之差都不超过 $K$，那么这个序列就被称作完美序列。
>
> 给定一个序列，求这个序列的最长完美子序列。

在值域上建树，每到一个值，找离它不超过 $K$ 的最大值，然后对这个值修改一下就行了。

权值线段树应用广泛，但其实线段树本身没啥特别的，树操作部分代码就不放了。

```cpp
const int MAXN(2e5+5);
int a[MAXN],WS[MAXN];
int main()
{
    int n,K;
    cin>>n>>K;
    pnode rt{new tnode};
    for (int i{1};i<=n;++i)
        scanf("%d",a+i),WS[i]=a[i];
    sort(WS+1,WS+n+1);auto wend=unique(WS+1,WS+n+1); // 离散化，权值线段树常用操作，也可以动态开点
    build(rt,1,wend-WS);
    int ans{0};
    for (int i{1};i<=n;++i)
    {
        int qa{lower_bound(WS+1,wend,a[i]-K)-WS},qb{upper_bound(WS+1,wend,a[i]+K)-WS};
        int as{query(rt,qa,qb)+1};ans=max(ans,as);
        modify(rt,lower_bound(WS+1,wend,a[i])-WS,as); // 我用的左闭右开区间，询问给的闭，稍微处理一下
    }
    printf("%d\n",ans);
    return 0;
}
```

## 标记永久化

有些时候标记下传代价很大，比如这道题：

> 给定一个序列，这个序列上每个位置上是一个集合
> - 区间每个集合里插入一个数
> - 询问区间所有集合合并后的严格第 $K$ 小

因为值域小（$\leq 5000$），所以对于每一个结点开一个 bitset 维护这个区间的集合的交。

但是这个题下传代价大概是这样的：

```cpp
// Code by XiaoZeCheng
t[p<<1].dat |= t[p].laz; t[p<<1|1].dat |= t[p].laz;
t[p<<1].laz |= t[p].laz; t[p<<1|1].laz |= t[p].laz;
t[p].laz.set(0);
```

所以要稍稍优化一下，改成这种永久化的形式，每次查询时一路或上所有标记。

最终的代码还是很简洁的。

```cpp
using Int=bitset<5005>;
struct tnode
{
    using pnode=tnode*;
    int L,R;
    pnode l,r;
    Int val,tg;
    void tag(int x){val[x]=tg[x]=1;}
};
using pnode=tnode::pnode;
void build(pnode p,int L,int R)
{
    p->L=L;p->R=R;
    if (L+1==R) return;
    int mid{L+R>>1};
    build(p->l=new tnode,L,mid);
    build(p->r=new tnode,mid,R);
}
void upd(pnode p,int L,int R,int x)
{
    if (L<=p->L&&p->R<=R) return p->tag(x);
    if (R<=p->L||p->R<=L) return;
    p->val[x]=1;
    upd(p->l,L,R,x);upd(p->r,L,R,x);
}
Int query(pnode p,int L,int R)
{
    if (L<=p->L&&p->R<=R) return p->val;
    if (R<=p->L||p->R<=L) return Int{};
    return query(p->l,L,R)|query(p->r,L,R)|p->tg; // 这里，或上每层的标记
}
```

## 建图工具

[例题](https://www.luogu.com.cn/problem/P3588)

如果，需要从一个区间上的点连到一个点，可以考虑把线段树上组成这个区间的点（即：查询时返回整个区间信息的结点）连到目标点，大大降低了时空复杂度。

例题还没做出来，代码就不放了。

## 动态开点

假设我们要建一颗值域线段树。空间复杂度 $\Theta(V)$。

如果值域过大，可以考虑不 `build`，而是每个结点在需要用的时候再分配。

摊还空间复杂度 $o(\lg V)$，单次最坏 $\Theta(\lg n)$。

动态开点一般要求初值不特殊。又因为动态开点线段树不完整，所以一般结点不维护区间 `[L,R)`，而是将信息放在递归中，方便建新结点。

[普通平衡树](https://www.luogu.com.cn/problem/P3369)

实现的时候要注意：

- 解引用空指针比较麻烦，建议建哨兵并将 `cnt` 设为 `0`；
- 注意查前驱后继时的一些判断（其实任何写法都要注意）。

```cpp
struct tnode
{
    tnode *l,*r;
    int cnt;
    tnode(int c=0):cnt{c}{}
};
using pnode=tnode*;
pnode null{new tnode};
void check(pnode& p){if(p!=null)return;p=new tnode;p->l=p->r=null;}
// k=1为插入，k=-1为删除
void modify(pnode& p,int x,int k,int L=-1e7,int R=1e7)
{
    check(p);
    p->cnt+=k;
    if (L==x&&R==x+1) return;
    int mid{L+R>>1};
    if (x<mid)
        modify(p->l,x,k,L,mid);
    else
        modify(p->r,x,k,mid,R);
}
int ex;
// 返回小于x的数个数，ex为x有多少个
int Rank(pnode& p,int x,int L=-1e7,int R=1e7)
{
    check(p);
    if (L==x&&R==x+1)
        return ex=p->cnt,0;
    int mid{L+R>>1};
    if (x<mid)
        return Rank(p->l,x,L,mid);
    else
        return p->l->cnt+Rank(p->r,x,mid,R);
}
int kth(pnode& p,int k,int L=-1e7,int R=1e7)
{
    check(p);
    if (L==R-1) return L;
    int mid{L+R>>1};
    if (k<=p->l->cnt)
        return kth(p->l,k,L,mid);
    else
        return kth(p->r,k-p->l->cnt,mid,R);
}
// 前后驱可由 kth+rank 简单实现
```

动态开点的空间复杂度为 $O(\min(m\lg n,n))$，且一般低于这个上界。

## 线段树合并

线段树合并是一种有趣的技巧，通常和动态开点同时使用。

有两种合并方式：

- 将 $b$ 合并到 $a$ 上（会破坏原先两颗树的结构）；
- 合并到新树上（做法类似主席树的合并方式）。

这里给出方式一的实现。

> 给定一棵树，每个结点有一个能力值 $w_u$。
> 求对于每个结点 $u$，其子树内满足 $w_v>w_u$ 的结点 $v$ 个数。

对于每个结点，递归处理它所有儿子，然后合并所有儿子，再加上自己后查询即可。

```cpp
// 动态开点权值线段树
struct tnode
{
    tnode *l,*r;
    int cnt;
};
using pnode=tnode*;
pnode nil{new tnode{nil,nil,0}};
void check(pnode& p){if(p==nil)p=new tnode{nil,nil,0};}
// 单点修改
void modify(pnode& p,int k,int cnt)
{
    check(p);++p->cnt;
    if (cnt==1) return;
    int cq{cnt>>1};
    if (k<=cq) modify(p->l,k,cq);
    else modify(p->r,k-cq,cnt-cq);
}
void merge(pnode& p,pnode q)
{
    if (q==nil) return;
    if (p==nil)
    {
        p=q;
        return;
    }
    p->cnt+=q->cnt;
    merge(p->l,q->l);merge(p->r,q->r);
    delete q;
}
// 查询比某个点大的数个数
int query(pnode p,int k,int L,int R)
{
    if (p==nil||L>k) return p->cnt;
    int mid{L+R>>1};
    if (k>=mid-1) return query(p->r,k,mid,R);
    else return p->r->cnt+query(p->l,k,L,mid);
}
pnode fz(int u)
{
    pnode p{nil};
    // e存储树，r存储答案
    for (auto v:e[u])
        merge(p,fz(v));
    r[u]=query(p,v[u],1,1e9+1);
    modify(p,v[u],1e9);
    return p;
}
```

一般线段树合并都是在类似如上的操作树中进行的，但也有某些不可逆的序列操作适用线段树合并，比如[这题](https://www.luogu.com.cn/problem/P3201)。

维护答案，每次修改时合并线段树，并更新全局答案。

这种将所有 $x$ 改成 $y$ 就属于不可逆的操作（可以去爬第二分块）。

```cpp
#include <bits/stdc++.h>
using namespace std;
struct tnode
{
    tnode *l,*r;
    int cnt;
    bool beg,end;
    inline tnode();
    void upd(){cnt=l->cnt+r->cnt-(l->end&&r->beg);beg=l->beg;end=r->end;}
};
using pnode=tnode*;
pnode nil{new tnode};
tnode::tnode():l{nil},r{nil},cnt{0},beg{false},end{false}{}
inline void check(pnode& p){if(p==nil||p==nullptr)p=new tnode;}
void modify(pnode& p,int k,int cnt)
{
    check(p);
    if (cnt==1) {p->cnt=p->beg=p->end=1;return;}
    int cp{cnt>>1};
    if (k<=cp) modify(p->l,k,cp);
    else modify(p->r,k-cp,cnt-cp);
    p->upd();
}
void merge(pnode& p,pnode q,int cnt)
{
    if (q==nil) return;
    if (p==nil) {p=q;return;}
    if (cnt==1)
        p->cnt=p->beg=p->end=(p->cnt||q->cnt);
    else
    {
        merge(p->l,q->l,cnt>>1);merge(p->r,q->r,cnt-(cnt>>1));
        p->upd();
    }
    delete q;
}
const int N(1e5),V(1e6);
pnode seg[V+5];
int main()
{
    int n,m;cin>>n>>m;
    int ans{0};
    for (int i{0};i<n;++i)
    {
        int color;scanf("%d",&color);
        check(seg[color]);
        ans-=seg[color]->cnt;
        modify(seg[color],i+1,n);
        ans+=seg[color]->cnt;
    }
    while (m--)
    {
        int op,x,y;scanf("%d",&op);
        if (op==1)
        {
            scanf("%d %d",&x,&y);
            if (x==y) continue;
            check(seg[x]);check(seg[y]);
            ans-=seg[x]->cnt+seg[y]->cnt;
            merge(seg[y],seg[x],n);
            ans+=seg[y]->cnt;
            // 一定要注意清空被merge的线段树！
            seg[x]=nil;
        }
        else printf("%d\n",ans);
    }
    return 0;
}
```