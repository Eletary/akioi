# 链表

链表是一种高效插入与删除的数据结构。

## 简单应用

咕咕咕

## 进阶玩法

用数组模拟而非 `std::list` 有一个好处：可以做区间删除而不用考虑内存泄漏。

同时，如果建立序列链表，甚至可以 $O(1)$ 访问每个位置。

> 给定 $1$ 到 $n$ 的一个排列 $a$， 求 $\sum_{l=1}^n\sum_{r=l}^n f(l,r,k)$，其中 $f(l,r,k)$ 表示 $a_{[l,r]}$ 的第 $k$ 大值

考虑在序列上建立一个链表。对于每个从小到大的值，向左向右各爬 $k$ 格，然后将当前点从链表删除。这样每次爬的时候所有值都比自己大。

复杂度 $\Theta(nk)$。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N(5e5);
int lp[N+2],rp[N+2],p[N+1];
int main()
{
    int T;cin>>T;
    while (T--)
    {
        int n,k;cin>>n>>k;
        for (int i{1};i<=n;++i)
        {
            int a;scanf("%d",&a);
            p[a]=i;
            lp[i]=i-1;rp[i]=i+1;
        }
        lp[0]=0;rp[n+1]=n+1;
        long long ans{0};
        for (int i{1};i<=n;++i)
        {
            int l{p[i]},r{p[i]};
            vector<int> dis;
            for (int j{0};j<k;++j) dis.push_back(l-lp[l]),l=lp[l];
            for (int j{0};j<k;++j)
            {
                ans+=(rp[r]-r)*dis[k-j-1]*i;
                r=rp[r];
            }
            rp[lp[p[i]]]=rp[p[i]];
            lp[rp[p[i]]]=lp[p[i]];
        }
        cout<<ans<<endl;
    }
    return 0;
}
```