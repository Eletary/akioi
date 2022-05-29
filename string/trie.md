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