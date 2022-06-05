# 丹钓数据结构

## 丹钓战

单调栈是一种十分简单的数据结构，一般用于求每个点最近的违反单调性质的点。

> 如给定序列 $a$，求 $\sum_{i=1}^n C(i)$，
其中 $C(i)$ 表示最小的 $j>i$，使得 $a_j\geq a_i$。

一种做法是 $i$ 无法弹出的 $j$ 都满足 $C(j)>i$，以此逆向思考。

```cpp
#include <bits/stdc++.h>
using namespace std;
stack<int> qwq;
int main()
{
    int n;cin>>n;
    long long ans{0};
    for (int i{1};i<=n+1;++i)
    {
        int x;
        if (i==n+1) x=0x3f3f3f3f;
        else scanf("%d",&x);
        while (qwq.size()&&qwq.top()<=x)
            qwq.pop();
        ans+=qwq.size();
        qwq.emplace(x);
    }
    cout<<ans<<endl;
}
```

同时，单调栈还有性质：如果 $i$ 能弹出 $j$，那么 $C(j)=i$。

```cpp
#include <bits/stdc++.h>
using namespace std;
stack<pair<int,int>> qwq;
int main()
{
    int n;cin>>n;
    long long ans{0};
    for (int i{1};i<=n+1;++i)
    {
        int x;
        if (i==n+1) x=0x3f3f3f3f;
        else scanf("%d",&x);
        while (qwq.size()&&qwq.top().second<=x)
        {
            ans+=i-qwq.top().first-1;
            qwq.pop();
        }
        qwq.emplace(i,x);
    }
    cout<<ans<<endl;
}
```

这种做法更具有推广意义。

> 给出一张柱状图，第 $i$ 根柱子的宽度为 $1$，高度为 $h_i$，现在请你求出柱状图中最大矩形的面积。

对于每一个 $i$，求出最大的 $j$ 使得 $h_{(i,j]}\geq h_i$，最小的  $j$ 同理，最后乘起来比较下即可。

前一题的性质 2 可以做。这里还有一种方法：第一个未被 $i$ 弹出的 $j$ 是第一个 $h_j<h_i$ 的，减一即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
stack<int> qwq,wfh;
const int MAXN(1e5);
int a[MAXN+5],L[MAXN+5],R[MAXN+5];
int main()
{
    int n;cin>>n;
    for (int i{1};i<=n;++i) scanf("%d",a+i);
    a[0]=-1;qwq.push(0);
    for (int i{1};i<=n;++i)
    {
        while (a[qwq.top()]>=a[i]) qwq.pop();
        L[i]=qwq.top();
        qwq.push(i);
    }
    qwq=wfh;
    qwq.push(n+1);a[n+1]=-1;
    for (int i{n};i>=1;--i)
    {
        while (a[qwq.top()]>=a[i]) qwq.pop();
        R[i]=qwq.top();
        qwq.push(i);
    }
    long long ans{0};
    for (int i{1};i<=n;++i)
        ans=max(ans,1LL*(R[i]-L[i]-1)*a[i]);
    cout<<ans<<endl;
    return 0;
}
```

## 丹钓队列

单调队列是一种可以从 $[l,r)$ 的单调值扩展到 $[l,r+1$ 以及 $[l+1,r)$ 的数据结构。

> 对于长为 $n$ 的数列，求出所有长度为 $m$ 的区间的最大值。

模板题。

```cpp
#include <bits/stdc++.h>
using namespace std;
deque<int> q;
const int MAXN(1e5);
int a[MAXN+5];
void push(int x)
{
    while (q.size()&&a[q.back()]<=a[x]) q.pop_back();
    q.push_back(x);
}
void pop(int x)
{
    if (q.front()<x) q.pop_front();
}
int main()
{
    int n,m;cin>>n>>m;
    q.push(0);
    for (int i{1};i+m-1<=n;++i)
    {
        push(i);pop(i-m+1);
        if (i>=m) cout<<q.front()<<" ";
    }
    return 0;
}
```

> 求长度不超过 $m$ 的最大子段和。

经典题。转化为前缀和，对于每个数查询它之前的最小值即可。

注意必须选。

```cpp
...
void push(int x)
{
    while (q.size()&&a[q.back()]<=a[x]) q.pop_back();
    q.push_back(x);
}
...
int main()
{
    int n,m;
    cin>>n>>m;
    for (int i{1};i<=n;++i)
        scanf("%d",a+i);
    partial_sum(a+1,a+n+1,a+1); // 前缀和
    int ans(0xcfcfcfcf);
    for (int i{1};i<=n;++i)
    {
        pop(i-m);
        ans=max(ans,a[i]-a[q.front()]); // 因为区间不能为空，要查询完再插入
        push(i);
    }
    cout<<ans<<endl;
    return 0;
}
```

### 不定长维护

> 求序列中最长的子串，使得子串的极差不超过 $k$。

结合尺取，维护区间最大值和最小值即可（用于计算极差）。

这告诉我们，单调队列不一定维护定长。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN(3e6);
int h[MAXN+5];
deque<int> qm{1},qn{1};
void push(int x)
{
    while (qm.size()&&h[qm.back()]<=h[x]) qm.pop_back();
    qm.push_back(x);
    while (qn.size()&&h[qn.back()]>=h[x]) qn.pop_back();
    qn.push_back(x);
}
void pop(int x)
{
    if (qm.front()<x) qm.pop_front();
    if (qn.front()<x) qn.pop_front();
}
int main()
{
    int k,n;cin>>k>>n;
    for (int i{1};i<=n;++i) scanf("%d",h+i);
    int head{1},tail{2};int ans{1};
    while (tail<=n)
    {
        while (qm.empty()||qn.empty()) push(tail++);
        while (tail<=n&&h[tail]-h[qn.front()]<=k&&h[qm.front()]-h[tail]<=k)
            push(tail++);
         
        ans=max(ans,tail-head);
        pop(++head);
    }
    cout<<ans<<endl;
    return 0;
}
```