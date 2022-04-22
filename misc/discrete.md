# 离散化

## 前置芝士

- [AK IOI](/basic/akioi.md)

## 简介

离散化，就是当有些数据因为本身很大或者类型不支持，自身无法作为数组的下标来方便地处理，而影响最终结果的只有元素之间的相对大小关系时，我们可以将原来的数据按照从大到小编号来处理问题。

用来离散化的可以是大整数、浮点数、字符串等等。

## 实现

拿到数组后可以这么做：

```cpp
for (int i{1};i<=n;++i)
    scanf("%d",a+i),WS[i]=a[i];
sort(WS+1,WS+n+1);auto wend=unique(WS+1,WS+n+1);
```

查找映射：

```cpp
lower_bound(WS+1,wend,x)-WS
```

有些时候也可以直接将原数据替换为映射数据：

```cpp
for (int i{1};i<=n;++i)
    a[i]=lower_bound(WS+1,wend,a[i])-WS;
```

## 参考文献：

[OI-Wiki](https://oi-wiki.org/misc/discrete/)