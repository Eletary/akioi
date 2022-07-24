# 字符串哈希

字符串哈希，是一种将字符串映射到整数的方法，可以根据需要灵活采用多种不同的算法。

## 位值映射

这种映射方式将字符串理解为一个喵（我喜欢2579）进制数并取模（我喜欢998244353）。

这种哈希方式适用大多数场景。

上道经典：[最长公共前缀](https://hydro.ac/d/zcoj/p/P1016)

二分长度，哈希判断是否相等即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int bit{2579},b6e0{998244353};
class hstring:public string
{
    static const int MAXN{int(1e6+5)};
    int A[MAXN];
    int ppi[MAXN];
public:
    void flush()
    {
        A[0]=0;
        for (int i{1};i<=size();++i)
            A[i]=(1LL*A[i-1]*bit%b6e0+(operator[](i-1)-'a'))%b6e0;
        ppi[0]=1;
        for (int i{1};i<=size();++i)
            ppi[i]=1LL*ppi[i-1]*bit%b6e0;
    }
    int hash(int l,int r) {return ((A[r]-1LL*A[l-1]*ppi[r-l+1])%b6e0+b6e0)%b6e0;}
};
hstring s;
int l,r;
bool check(int x);
int main()
{
    {int T;cin>>T;}
    int T;cin>>T;
    cin>>s;
    s.flush();
    while (T--)
    {
        scanf("%d %d",&l,&r);--l;--r;
        if (l>r) swap(l,r);
        int L{0},R{s.size()-r},ans{0};
        while (L<=R)
        {
            int mid{(L+R)>>1};
            if (check(mid))
            {
                ans=mid;
                L=mid+1;
            }
            else R=mid-1;
        }
        printf("%d\n",ans);
    }
    return 0;
}
bool check(int x)
{
    return s.hash(l+1,l+x)==s.hash(r+1,r+x); 
}
```

[公共串](https://hydro.ac/d/zcoj/p/R1006)

和上题类似，二分长度哈希判重即可。

~~这题在 SPOJ 上有多倍经验~~

[A Horrible Poem](https://hydro.ac/d/zcoj/p/R1007)

还没过 咕咕咕

## 数值映射

与位置无关的映射，多用于判断两个串是否由相通字符组成。

[例题](https://hydro.ac/d/zcoj/p/R1008)

不是很好解释，上代码：

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N(3e5);
int a[N+5],val[N+5];
size_t hasher(int x)
{
    return (1LL*x*19260817+993244853)%1000000007;
}
size_t h[N+5],rsv[N+5];
void init(int n)
{
    // hasher=hash<int>{};
    for (int i{1};i<=n;++i)
    {
        h[i]=hasher(a[i])^h[i-1];
        rsv[i]=rsv[i-1]^hasher(i);
    }
}
size_t Hash(int l,int r){return h[r]^h[l-1];}
int main()
{
    int n;
    cin>>n;
    for (int i{1};i<=n;++i)
        scanf("%d",a+i);
    int ans{0};
    init(n);
    for (int i{1};i<=n;++i)
        // 排列必须包含1
        if (int mx{0};a[i]==1)
            for (int j{i};j<=n;++j)
            {
                // 有重复，不可能再有
                if (val[a[j]])
                {
                    for (int k{j-1};k>=i;--k) --val[a[k]];
                    break;
                }
                ++val[a[j]];mx=max(mx,a[j]);
                // 以当前位为结尾，是否能够组成一个排列
                ans+=(Hash(j-mx+1,j)==rsv[mx]);
            }
    fill_n(val+1,n,0);
    // 正反各跑一遍
    reverse(a+1,a+n+1);init(n);
    for (int i{1};i<=n;++i)
        if (a[i]==1)
        {
            int mx{0};val[1]=1;
            for (int j{i+1};j<=n;++j)
            {
                if (val[a[j]])
                {
                    for (int k{j-1};k>=i;--k) --val[a[k]];
                    break;
                }
                ++val[a[j]];mx=max(mx,a[j]);
                ans+=(Hash(j-mx+1,j)==rsv[mx]);
            }
        }
    cout<<ans<<endl;
    return 0;
}
```

复杂度为 $\Theta(2n-1)=\Theta(n)$。