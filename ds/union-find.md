# 并查集

并查集是一种维护不相交集合的数据结构。

## 前置芝士

- [AK IOI](/basic/akioi.md)

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

咕咕咕

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

首先压缩父亲，此时树上应该是 $a \rarr fa \rarr rt$。

显然已知 $f(a,fa),f(fa,rt)$。

结束了。

### 例题

一般这类题目都会给一堆两点之间的关系。

> 给定 $m$ 组关系 $a-b=c$，判断是否造假（后面的关系和前面冲突）。

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