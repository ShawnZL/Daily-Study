# Leetcode No.869

###### [869. 重新排序得到 2 的幂](https://leetcode-cn.com/problems/reordered-power-of-2/)

方法一：

直接先统计在 int 范围内的 2 的幂次，按照数字进行统计，统计每一个字母出现的次数，然后组合成为一个 string ,任何可以匹配的数字每一个总数固定，组合的string 都一样。再去判断

**有一个问题就是对于string类型，每一个char都可以当作int进行操作的！！！**

```c++
string countDigits(int n) {
    string cnt(10, 0);
    while (n) {
        ++cnt[n % 10];
        n /= 10;
    }
    return cnt;
}

unordered_set<string> powerOf2Digits;

int init = []() {
    for (int n = 1; n < 1e9; n <<= 1) { //注意 <<=
        powerOf2Digits.insert(countDigits(n));
    }
    return 0;
}();

class Solution {
public:
    bool reorderedPowerOf2(int n) {
        return powerOf2Digits.count(countDigits(n));
    }
};
```

