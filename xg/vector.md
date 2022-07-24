# vector

一种 STL 容器。一种与平衡树比肩的暴力数据结构。

编译器的变态优化、每次插入不一定在头部使其常数极小，$n^2$ 过百万不再是神话。

亲测以下代码不开 `O2` 就可以通过「模板」快速排序：

```cpp
#include <bits/stdc++.h>
using namesapce std;
int main()
{
    vector<int> v;
    int n;cin>>n;
    while (n--)
    {
        int a;cin>>a;
        v.insert(upper_bound(v.begin(),v.end(),a));
    }
    copy(v.begin(),v.end(),ostream_iterator<int>(cout," "));
    return 0;
}
```