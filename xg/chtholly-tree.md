# 珂朵莉树

一种维护一堆区间信息的暴力数据结构。

珂朵莉树可以高效维护包含大量区间赋值的区间操作。复杂度接近 $O(n)$，上界为 $\Theta(n\log \log n)$。

## split

```cpp
Iter split(int p)
{
    Iter it=t.lower_bound(node(p));
    if (it!=t.end()&&it->l==p) return it;
    --it;
    int l{it->l},r{it->r},v{it->v};
    t.erase(it);
    t.emplace(l,p,v);
    return t.emplace(p,r,v).first;
}
```

## assign

```cpp
void assign(int l,int r，int v)
{
    Iter itr{split(r)},itl{split(l)};
    t.erase(itl,itr);
    t.emplace(l,r,v);
}
```

其他操作随缘实现即可。