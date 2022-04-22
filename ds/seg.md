# 线段树

## 前置芝士
- [分治](/basic/conquer.md)

## 简介

线段树是一种利用分治维护区间的数据结构。

线段树是一棵伪完全二叉树（底层叶子节点不保证从左向右），每一个叶节点维护单位区间（序列上为一个元素），其他节点则维护两儿子信息的合并。
相当于就是一棵分治树。

## 建树

线段树的维护方式有两种：数组或类链表。

本文的例子都将会通过类链表的形式给出。

因为线段树的每一个节点都是一个区间，我们可以从上至下递归建树。

```cpp
struct tnode
{
    int L,R;
    tnode *l,*r;
    int mx; // 这些是要维护的东西，以 RMaxQ 为例
    void upd() {mx=max(l->mx,r->mx);} // 两儿子信息合并
};
int a[MAXN];
void build(tnode* p,int L,int R) // O(n) 分治建树
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

容易看出，线段树的空间复杂度为 $\Theta(n)$。

## 区间操作

### 单点修改 & 区间查询

- 将点 `k` 的值改为  `x`
- 查询区间 $[L,R)$ 的最大值

从根节点开始查找这个节点即可。

```cpp
void modify(tnode* p,int k,int x)
{
    if (p->L==k&&p->R-1=k)
    {
        p->mx=x;
        return;
    }
    if (k<p->L||p->R<=k) return;
    modify(p->l,k,x);modify(p->r,k,x);
    p->upd(); // 注意每次儿子被修改后要重新合并两儿子
}
```

单点查询也是类似的原理，就不放代码了。

从根递归。每到达一个节点时：

- 如果该节点被待查区间包含，则返回该节点维护的信息
- 如果该节点与待查区间不相交，则返回不影响答案的信息（如 RMaxQ 中返回极小值）
- 否则，递归两个儿子，返回它们结果的并。

代码如下：

```cpp
int query(tnode* p,int L,int R)
{
    if (L<=p->L&&p->R<=R) return p->mx;
    if (R<=p->L||p->R<=L) return 0; // 本题所有值为正数
    return max(query(p->l,L,R),query(p->r,L,R));
}
```
