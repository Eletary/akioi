# 可持久化数据结构

本文介绍基于更改的完全可持久化的数据结构。它的思想是每次复制修改的结点。

持久化不止可以应用于历史版本的题目，还相当于静态维护多个差距不大的结构。

## 线段树

大名鼎鼎的主席树。

线段树的各种操作是 $O(\lg n)$ 的，所以最多修改 $\Theta(\lg n)$ 个结点。

每次修改时新增一些结点，若某一个子树不变就仍指向原来的子树。

空间复杂度 $O(m\lg n)$。

### 数组

无脑模拟即可。

```cpp
// 此题卡空间
struct tnode
{
    union
    {
        struct {tnode *l,*r;};
        int val;
    };
    tnode(int n=0):val{n}{}
};
using pnode=tnode*;
// 此题卡空间
pnode new_tnode()
{
    static pnode t,p;
    const static size_t N{1000000};
    if (t==nullptr||p-t>=N) p=t=new tnode[N];
    return p++;
}
const int N(1e6);
pnode ver[N+5];
pnode build(int cnt)
{
    if (cnt==1) return new tnode{read()};
	pnode p=new_tnode();
    p->l=build(cnt>>1);
    p->r=build(cnt-(cnt>>1));
    return p;
}
// ver[i]=modify(ver[ve],p,v,n);
pnode modify(pnode p,int k,int x,int cnt)
{
    if (cnt==1) return new tnode{x};
    pnode q{new_tnode()};
    q->l=p->l;q->r=p->r;
    if (k<=cnt>>1) q->l=modify(p->l,k,x,cnt>>1);
    else q->r=modify(p->r,k-(cnt>>1),x,cnt-(cnt>>1));
    return q;
}
// printf("%d\n",query(ver[i]=ver[ve],p,n));
int query(pnode p,int k,int cnt)
{
    if (cnt==1) return p->val;
    if (k<=cnt>>1) return query(p->l,k,cnt>>1);
    else return query(p->r,k-(cnt>>1),cnt-(cnt>>1));
}
```

### 并查集

本质上是数组，如上实现即可。注意使用按秩合并，不能路径压缩。

## 合并

线段树合并的时候，复杂度比较玄学，但大致认为修改的结点不会太多。

这样合并时不会破坏原树。