# 乘法逆元

## 逆元简介

如果一个线性同余方程 $ax\equiv 1\pmod{b}$，则 $x$ 称为 $a\!\mod b$ 的逆元，记作 $a^{-1}$。

## 逆元计算

### exgcd

不会。咕咕咕。

### 快速幂

#### 前置芝士

- [快速幂](/math/quick-pow.md)

因为 $ax \equiv 1 \pmod b$；

所以 $ax \equiv a^{b-1} \pmod b$（根据[费马小定理](/math/number-theory/fermat.md)）

所以 $x \equiv a^{b-2} \pmod b$。

然后我们就可以用快速幂来求了。

```cpp
inline int qpow(long long a, int b) {
    int ans = 1;
    a = (a % p + p) % p;
    for (; b; b >>= 1) {
    if (b & 1) ans = (a * ans) % p;
    a = (a * a) % p;
    }
    return ans;
}
```

注意使用[费马小定理](/math/number-theory/fermat.md]需要限制 $b$ 是一个素数，而扩展欧几里得算法只要求 $\gcd(a, p) = 1$。

## 参考文献

- [OI-Wiki](https://oi-wiki.org/math/number-theory/inverse/)