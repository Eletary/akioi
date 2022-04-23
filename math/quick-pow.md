# 快速幂

## 前置芝士

- [位运算](/math/bit.md)

如果把 $n$ 写作二进制为 $(n_tn_{t-1}\cdots n_1n_0)_2$，那么有：

$$
n = n_t2^t + n_{t-1}2^{t-1} + n_{t-2}2^{t-2} + \cdots + n_12^1 + n_02^0
$$

其中 $n_i\in\{0,1\}$。那么就有

$$
\begin{aligned}
a^n & = (a^{n_t 2^t + \cdots + n_0 2^0})\\\\
& = a^{n_0 2^0} \times a^{n_1 2^1}\times \cdots \times a^{n_t2^t}
\end{aligned}
$$

根据上式我们发现，原问题被我们转化成了形式相同的子问题的乘积，并且我们可以在常数时间内从 $2^i$ 项推出 $2^{i+1}$ 项。

这个算法的复杂度是 $\Theta(\log n)$ 的，我们计算了 $\Theta(\log n)$ 个 $2^k$ 次幂的数，然后花费 $\Theta(\log n)$ 的时间选择二进制为 1 对应的幂来相乘。

## 代码实现

递归：

```cpp
long long qpow(long long a, long long b) {
  if (b == 0) return 1;
  long long res = qpow(a, b / 2);
  if (b % 2)
    return res * res * a;
  else
    return res * res;
}
```

非递归：

```cpp
long long qpow(long long a, long long b) {
  long long res = 1;
  while (b > 0) {
    if (b & 1) res = res * a;
    a = a * a;
    b >>= 1;
  }
  return res;
}
```

## 参考文献

本文来自 [OI-Wiki](https://oi-wiki.org/math/quick-pow/)。