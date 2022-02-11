# Leetcode No.50

###### [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

方法一：使用方法为分治法

如果我们要计算 $x^{64}$按照

x -> $x^2$ -> $x^4$ -> $x^8$ -> $x^{16}$ -> $x^{32}$ -> $x^{64}$

另一个例子就是77

x -> $x^2$ -> $x^4$ -> $x^9$ -> $x^{19}$ -> $x^{38}$ -> $x^{77}$

在 $x$ -> $x^{2}$ 和  $x^2$ -> $x^{4}$ 和  $x^{19}$ -> $x^{38}$ 这些步骤中，我们直接把上一次的结果平方，而在  $x^4$ -> $x^{9}$， $x^9$ -> $x^{19}$，和  $x^{38}$ -> $x^{77}$ 这些步骤中，我们除了要将结果平方以外，还要额外乘一个x。

直接从左到右进行推导看上去很困难，因为在每一步中，我们不知道在将上一次的结果平方之后，还需不需要额外乘 x*。但如果我们从右往左看，分治的思想就十分明显了：

当我们要计算 $x^n$时，我们可以先递归地计算出 $y=x^{⌊n/2⌋}$。

根据递归计算的结果，如果 n 为偶数，那么 $x^n$ = $y^2$ ；如果 n 为奇数，那么 $x^n$ = $y^2$ * x;

递归的边界为 n = 0，任意数的0次方均为1。

```
class Solution {
public:
    double quickMul(double x, long long N) {
        if (N == 0)
            return 1.0;
        double y = quickMul(x, N/2);
        return N % 2 == 0 ? y * y : y * y * x;
    }
    double myPow(double x, int n) {
        long long N = n;
        return N >= 0 ? quickMul(x, N) : 1.0 / quickMul(x, -N);
    }
};
```

