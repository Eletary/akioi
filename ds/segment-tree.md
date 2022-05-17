# 线段树

## 简介

线段树是一种利用分治维护区间的数据结构。

线段树是一棵伪完全二叉树（底层叶子节点不保证从左向右），每一个叶节点维护单位区间（序列上为一个元素），其他节点则维护两儿子信息的合并。
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

## 值域建树

> 如果一个序列，它的任意两个相邻的元素之差都不超过 $K$，那么这个序列就被称作完美序列。
>
> 给定一个序列，求这个序列的最长完美子序列。

在值域上建树，每到一个值，找离它不超过 $K$ 的最大值，然后对这个值修改一下就行了。

值域线段树应用广泛，但其实线段树本身没啥特别的，树操作部分代码就不放了。

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
    sort(WS+1,WS+n+1);auto wend=unique(WS+1,WS+n+1); // 离散化，值域建树常用操作
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

因为值域小（$\leq 5000$），所以对于每一个节点开一个 bitset 维护这个区间的集合的交。

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

如果，需要从一个区间上的点连到一个点，可以考虑把线段树上组成这个区间的点（即：查询时返回整个区间信息的节点）连到目标点，大大降低了时空复杂度。

例题还没做出来，代码就不放了。

## 动态开点

假设我们要建一颗值域线段树。空间复杂度 $\Theta(V)$。

如果值域过大，可以考虑不建树，而是每个节点在需要用的时候在分配。

摊还空间复杂度 $o(\lg V)$，单次最坏 $\Theta(\lg n)$。

动态开点一般要求初值不特殊。

[普通平衡树](https://www.luogu.com.cn/problem/P3369)

实现的时候要注意：

- 解引用空指针比较麻烦，建议建哨兵并将 `cnt` 设为 `0`；
- 注意查前驱后继时的一些判断（其实任何写法都要注意）。

```cpp
#include <bits/stdc++.h>
using namespace std;
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