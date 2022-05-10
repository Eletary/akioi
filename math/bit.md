# 位运算

## 介绍

| 运算 | 运算符 | 数学符号表示                   |                 解释                  |
| ---- | :----: | ------------------------------ | :-----------------------------------: |
| 与   |  `num1&num2`   | $\And$、$\operatorname{and}$     |   只有两个对应位都为 $1$ 时才为 $1$   |
| 或   |  `num1\|num2`  | $\mid$、$\operatorname{or}$    | 只要两个对应位中有一个 $1$ 时就为 $1$ |
| 异或 |  `num1^num2`   | $\oplus$、$\operatorname{xor}$ |     只有两个对应位不同时才为 $1$      |
| 取反 |  `~num`   | 无 | 把所有的位翻转 |
| 左移 |  `num << i`  | 无 | 将 $num$ 的二进制表示向左移动 $i$ 位 |
| 右移 |  `num >> i`  | 无 | 将 $num$ 的二进制表示向右移动 $i$ 位 |

移位运算中如果出现如下情况，则其行为未定义：
1. 右操作数（即移位数）为负值
2. 右操作数大于等于左操作数的位数

## bitset

位运算操作数过大时，可以使用 STL 提供的 `bitset` 进行优化。

`bitset` 时空复杂度均为 $\Theta\left(\frac{n}{w}\right)$（$w$ 为位数，一般为 32/64），有巨大的常数优势。

以下为 `bitset` 的常用操作：

| 操作 | 作用 |
| :---: | :-: |
| `b.count()` | 统计 $1$ 的个数 |
| `b.any()` | 不为 $0$ 则返回 $true$ |
| `b.none()` | `!b.any()` |
| `b.set(x)` |  将所有位设为 $1$，传参则参数位设为 $1$ |
| `b.reset(x)` | 同上，不同的是设为 $0$ |
| `b.flip(x)` | 同上，不同的是翻转 |
| `b._Find_first()`| 找到低位起第一个 $1$ 的下标 |
| `b._Find_next(i)` | 找到自下标 $i$ 起的第一个 $1$（不包括 $i$）|
| `cout << b` | `cout` 支持 `bitset` |
| `== / !=` | 判断相不相等 |
| 六条位运算 | 见上表 |

## 应用

### 表示集合

咕咕咕

### Shift-And/Or

一种有趣的、利用位掩码表示信息/进行匹配的算法。

> 一个仅由 $0∼9$ 字符组成的字符串 $A$ 有 $n$ 个字符，$i$ 位置可选择的字符有 $a_i$ 种，给出一个长串 $B$，找出 $A$ 能构成的 $B$ 的所有子串。

Shift-And 代码：

```cpp
#include <bits/stdc++.h>
using namespace std;
using BT=bitset<1005>;
BT bt[10],ct[10];
int main()
{
    int n;cin>>n;
    for (int i{1};i<=n;++i)
    {
        int k;scanf("%d",&k);
        for (int j{1};j<=k;++j)
        {
            int w;scanf("%d",&w);
            bt[w][i]=1;
        }
    }
    string anb;cin>>anb;anb="$"+anb;
    BT ans;
    for (int i{1};i<=anb.size()-1;++i)
    {
        ans<<=1;ans.set(1);
        ans&=bt[anb[i]^48];
        if (ans[n]) printf("%d\n",i-n); 
    }
    return 0;
}
```

Shift-Or 代码（对 Shift-And 进行了简单优化）：

```cpp
#include <bits/stdc++.h>
using namespace std;
using BT=bitset<1005>;
BT bt[10],ct[10];
int main()
{
    int n;cin>>n;
    for (int i{0};i<10;++i) bt[i].set();
    for (int i{1};i<=n;++i)
    {
        int k;scanf("%d",&k);
        for (int j{1};j<=k;++j)
        {
            int w;scanf("%d",&w);
            bt[w][i]=0;
        }
    }
    string anb;cin>>anb;anb="$"+anb;
    BT ans;ans.set();
    for (int i{1};i<=anb.size()-1;++i)
    {
        ans=ans<<1|bt[anb[i]^48];
        if (ans[n]==0) printf("%d\n",i-n); 
    }
    return 0;
}
```