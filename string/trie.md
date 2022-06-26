# 字典树

Trie 树，或字典树，是一种专门用于维护多个字符串的数据结构。

这里给出一个不完全封装的指针版本 `insert`。

```cpp
struct trie
{
    trie* son[26];
    // 根到当前这个字符串的个数，可以维护其他东西
    int num;
    trie(){memset(son,0,sizeof son);}
};
using prie=trie*;
void Insert(prie p,const string& s)
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
```

## 01Trie

01Trie 维护整数。它把每个数的二进制序列当字符串存储。

01Trie 可以写普通平衡树，还能维护异或和等奇怪东西。

01Trie 可以被认为权值线段树的一种特化。但由于诡异的分治方式，01Trie 可以维护更多的有趣信息。

## 例题

[Xor Sum](https://hydro.ac/d/zcoj/p/R1003)

01Trie 无脑模拟即可。

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
struct TrieNode
{
    TrieNode* son[2];
    TrieNode()
    {
        memset(son,0,sizeof son);
    }
};
class trie
{
public:
    using node=TrieNode;
private:
    node rt;
public:
    void insert(int x)
    {
        node* cur{&rt};
        for (int i{1<<30};i;i>>=1)
        {
            bool b{x&i};
            if (cur->son[b]==nullptr) cur->son[b]=new node;
            cur=cur->son[b];
        }
    }
    int find(int x)
    {
        x=~x;
        int ans{0};
        node* cur{&rt};
        for (int i{1<<30};i;i>>=1)
        {
            bool b{x&i};
            if (cur->son[b]==nullptr) b=!b;
            ans=(ans<<1)|b;
            cur=cur->son[b];
        }
        return ans;
    }
};
```

[The xor-longest Path](https://hydro.ac/d/zcoj/p/R1005)

与上一题类似，因为 $(a,b)=(a,\text{root})\oplus(\text{LCA,root})\oplus(b,\text{root})\oplus(\text{LCA,root})=(a,\text{root})\oplus(b,\text{root})$，只要预处理每个点到根的异或值，扔进一个 Trie 里，然后枚举每个点，在 Trie 里找到其异或的最大值，最后取下 $\max$ 就好了。

复杂度 $\Theta(n\lg V)$。

[L语言](https://hydro.ac/d/zcoj/p/R1004)

递推。令 $f_i$ 表示前 $i$ 位的前缀可以被理解，则在 Trie 里沿着第 $i$ 位向前走，如果第 $j$ 位组成完整的单词就令 $f_j=\text{true}$。

```cpp

int main()
{
	int n,m;
    scanf("%d %d",&n,&m);
    trie tree;
    while (n--)
    {
        string s;cin>>s;
        tree.insert(s);
    }
    while (m--)
    {
		int ans{0};
        string s;cin>>s;
        memset(f,0,sizeof f);f[0]=true;
        for (int i{0};i<=s.size();++i)
        if (f[i])
		{
			ans=i;
			trie::iterator cur{tree.root()};
            // 核心部分
			for (int j{i};j<s.size()&&cur!=nullptr;++j)
			{
				int b{s[j]-'a'};
				cur=cur->son[b];
				if (cur!=nullptr&&cur->mark) f[j+1]=true;
			}
		}
		cout<<ans<<endl;
    }
	return 0;
}
```