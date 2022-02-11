# Leetcode No.686

###### [686. 重复叠加字符串匹配](https://leetcode-cn.com/problems/repeated-string-match/)

方法一：使用字符串hash

```c++
class Solution {
    using ull = unsigned long long;
    const ull m = 97755331;
public:
    int repeatedStringMatch(string a, string b) {
        int n1 = a.size(), n2 = b.size(), n3 = (n2 / n1 + 2) * n1;
        ull p = 1, hash = 0, val = 0;

        for (int i = 0; i < n2; ++i) {
            hash = hash * m + a[i % n1];
            val = val * m + b[i];
            p *= m;
        }
        if (hash == val) return (n2 - 1) / n1 + 1;

        for (int r = n2; r < n3; ++r) {
            hash = hash * m + a[r % n1] - a[(r - n2) % n1] * p;
            if (hash == val) return (r) / n1 + 1;
        }
        return -1;

    }
};
```

