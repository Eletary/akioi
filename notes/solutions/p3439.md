# Kpm的MC密码 题解

建立一颗后缀 Trie。易得每个结点的答案为其子树内的编号第 $k$ 小。

在 Trie 上做线段树合并即可。

注意**可能有多个相同的字符串**。

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
const int N(1e5);
struct tnode
{
    tnode *l,*r;
    int cnt;
};
using pnode=tnode*;
pnode nil{new tnode{nil,nil,0}},rt[N];
inline void check(pnode& p){if(p==nil)p=new tnode{nil,nil,0};}
void modify(pnode& p,int k,int cnt)
{
    check(p);++p->cnt;
    if (cnt==1) return;
    int cp{cnt>>1};
    if (k<=cp) modify(p->l,k,cp);
    else modify(p->r,k-cp,cnt-cp);
}
void merge(pnode& p,pnode q)
{
    if (q==nil) return;
    if (p==nil)
    {
        p=q;
        return;
    }
    p->cnt+=q->cnt;
    merge(p->l,q->l);merge(p->r,q->r);
    delete q;
}
int kth(pnode p,int k,int cnt)
{
    if (k>p->cnt) return 0xcfcfcfcf;
    if (cnt==1) return 1;
    if (k<=p->l->cnt) return kth(p->l,k,cnt>>1);
    else return (cnt>>1)+kth(p->r,k-p->l->cnt,cnt-(cnt>>1));
}
struct trie
{
    trie* son[26];
    vector<int> id;
    trie(){memset(son,0,sizeof son);}
};
using prie=trie*;
void Insert(prie p,const string& s,int id)
{
    for (char c:s)
    {
        c-='a';
        if (p->son[c]==nullptr)
            p->son[c]=new trie; 
        p=p->son[c]; 
    }
    p->id.push_back(id);
}
int res[N+5];
pnode fz(prie p)
{
    pnode q{nil};
    for (int i{0};i<26;++i)
        if (p->son[i]!=nullptr)
            merge(q,fz(p->son[i]));
    if (p->id.size())
    {
        for (auto x:p->id)
        {
            modify(q,x,n);
        }
        for (auto x:p->id)
        {
            res[x]=kth(q,res[x],n);
        }
    }
    return q;
}
int main()
{
    cin>>n;
    prie T{new trie};
    for (int i{1};i<=n;++i)
    {
        string s;cin>>s;
        reverse(s.begin(),s.end());
        Insert(T,s,i);
    }
    for (int i{1};i<=n;++i) cin>>res[i];
    fz(T);
    for (int i{1};i<=n;++i) cout<<(res[i]<0?-1:res[i])<<"\n";
    cout<<endl;
    return 0;
}
```