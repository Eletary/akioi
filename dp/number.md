# 数位 DP

数位 DP 是一种含有一维当前数位的 DP。

例如 $f_{i,j}$ 表示第 $i$ 位（从高到低），上一为 $j$ 的答案个数。

大多数数位 DP 为十进制。
 
数位 DP 题一般为求区间内满足某条件的数的个数，所以很多时候都可以分块打表。

数位 DP 一般以记忆化搜索的形式实现。

## 例题

> 定义一种不降数，这种数字必须满足从左到右各位数字成小于等于的关系，现在指定一个整数闭区间 $[a,b]$，问这个区间内有多少个不降数。

数位 DP 入门题。这里给出一份模板。

```cpp
#include <bits/stdc++.h>
using namespace std;
int DIG[15];
int f[15][10];
// pos: 当前位数 pre: 上一位的值 limit: 当前是否受限（就是能否自由取值）
// 因为求值有一个上界，所以存在 limit（所求值当前位上界）
int fz(int pos,int pre,int limit)
{
    if (pos<0) return true;
    // 只记忆化不受限的内容
    if (!limit&&f[pos][pre]!=-1) return f[pos][pre];
    int end{limit?DIG[pos]:9},ret{0};
    for (int i{pre};i<=end;++i)
        ret+=fz(pos-1,i,limit&&(i==end));
    return ret;
}
// 求解 [1,x]
int solve(int x)
{
    memset(f,-1,sizeof f);
    int cnt{0};
    while (x)
    {
        // 分数位存储
        DIG[cnt++]=x%10;
        x/=10;
    }
    return fz(cnt-1,0,true);
}
int main()
{
    int a,b;
    while (cin>>a>>b)
        // 前缀和简化问题
        cout<<solve(b)-solve(a-1)<<endl;
    return 0;
}
```